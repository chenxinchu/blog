---
title: JavaScript实现私有属性
date: 2020-03-01 22:31:44
tags:
---

## 前言
JavaScript作为一门面向对象的语言，却不像Java那样可以实现私有属性，一直为大家所诟病。下面简单介绍几种JavaScript实现私有属性的方式
<br/>

## 1. 基于代码规范
这里实现了一个钱包类，里面的元素和方法分别简单介绍一下。
- _id ：钱包创建的唯一ID
- _createTime ：钱包创建的时间
- _balance ：钱包里的金额
- _balanceLastModifiedTime ：最后一次修改金额的时间
- getId() ：获取ID
- getBalance() ：获取金额
- getCreateTime() ：获取钱包创建时间
- getBalanceLastModifiedTime() ：获取金额最后一次修改的时间
- increaseBalance(increasedAmount) ：往钱包中存入金额
- decreaseBalance(decreasedAmount) ：取出金额使用

直接上代码

```bash
const UUID = require('uuid'); 

class Wallet {
  constructor(){
    this._id = UUID.v1().replace(/-/g, ""); // 唯一id
    this._createTime = + new Date(); // 账户创建时间
    this._balance = 0 // 金额 初始值 
    this._balanceLastModifiedTime = +new Date(); // 金额修改时间
  }
  
  getId() {
    return this._id;
  }
  getBalance() {
    return this._balance; 
  }
  getCreateTime() { 
    return this._createTime;
  }
  getBalanceLastModifiedTime() { 
    return this._balanceLastModifiedTime;
  }

  
  increaseBalance(increasedAmount) {
    this._balance += increasedAmount;
    this._balanceLastModifiedTime = + new Date();
  }
  decreaseBalance(decreasedAmount) {

    if (decreasedAmount > this._balance) { // 花不起
      throw new Error('没有足够的钱');
    }
    this._balance -= decreasedAmount;
  }
}

const myWallet = new Wallet();
console.log(myWallet.getId()); // 1ad047805bbf11eaa550ab02c68e3a5b
console.log(myWallet.id); // undefined

```
上述方法只是一种规范，是一种约定俗成的方法，很容易被打破，而且也没有实现私有属性，可以直接访问_id元素并随意修改。
```bash
console.log(myWallet._id; // 1ad047805bbf11eaa550ab02c68e3a5b
myWallet._id = '10086'
console.log(myWallet.getId()); // 10086
```
<br/>

## 2. 通过立即执行函数，形成闭包

```bash
const UUID = require('uuid'); 

var Wallet = (function() {
  var id,createTime,balance, balanceLastModifiedTime; 
  return function() { //闭包
    id = UUID.v1().replace(/-/g, "");
    createTime = + new Date();
    balance = 0;
    balanceLastModifiedTime = +new Date();

    function checkAmount(amount) {
      if (isNaN(amount)) { // 是否为数字
        return false
      }
      return true;
    }

    return { //public 
      desc: '钱包',
      getId: function() {
        return id;
      },
      getCreateTime: function() {
        return createTime;
      },
      getBalanceLastModifiedTime: function() {
        return balanceLastModifiedTime;
      },
      getBalance: function() {
        return balance;
      },
      increaseBalance: function(increasedAmount) {
        if (!checkAmount(increasedAmount)) {
          throw new Error('发生错误');
        }
        balance += increasedAmount;
        balanceLastModifiedTime = +new Date();
      },
      decreateBalance: function(decreasedAmount) {
        if (!checkAmount(decreasedAmount)) {
          throw new Error('错误的金额')
        }
        if (decreasedAmount > this._balance) { 
          throw new Error('没有足够的钱');
        }
        balance -= decreasedAmount;
        balanceLastModifiedTime = +new Date();
      }
    }
  }
})()

const myWallet = new Wallet();
console.log(myWallet.getId()); // 1ad047805bbf11eaa550ab02c68e3a5b
console.log(myWallet.id); // undefined
```
这种方法的优点是实现了私有属性，myWallet实例并不能直接访问id，只能通过函数getId()来获取。在实际开发中，大部分的开发者采用该方式。  
但这种方式也存在一些缺陷，通过instanceof可知，myWallet实例并不是由Wallet生成
```bash
console.log(myWallet instanceof Wallet); // false
```
<br/>

## 3. 基于WeakMap
- WeakMap 是一种弱引用散链表，使用对象类型作为key值
```bash
const UUID = require('uuid');

var Wallet = (function () {
  var privateData = new WeakMap();
  function Wallet() {
    privateData.set(this, {
      id: UUID.v1().replace(/-/g, ""),
      createTime: new Date(),
      balance: 0,
      balanceLastModifiedTime: +new Date()
    })
  }

  function checkAmount(amount) {
    if (isNaN(amount)) { 
      return false
    }
    return true;
  }

  Wallet.prototype.getId = function () {
    return privateData.get(this).id;
  }
  Wallet.prototype.getCreateTime = function () {
    return privateData.get(this).createTime;
  }
  Wallet.prototype.getBalanceLastModifiedTime = function () {
    return privateData.get(this).balanceLastModifiedTime;
  }
  Wallet.prototype.getBalance = function () {
    return privateData.get(this).balance;
  }

  Wallet.prototype.increaseBalance = function (increasedAmount) {
    if (!checkAmount(increasedAmount)) {
      throw new Error('发生错误');
    }
    privateData.get(this).balance += increasedAmount;
    privateData.get(this).balanceLastModifiedTime = +new Date();
  }
  Wallet.prototype.decreateBalance = function (decreasedAmount) {
    if (!checkAmount(decreasedAmount)) {
      throw new Error('错误的金额')
    }
    if (decreasedAmount > privateData.get(this).balance) {
      throw new Error('没有足够的钱');
    }
    privateData.get(this).balance -= decreasedAmount;
    privateData.get(this).balanceLastModifiedTime = +new Date();
  }
  return Wallet;
}());

const myWallet = new Wallet();
console.log(myWallet.id); // undefined
console.log(myWallet.getId()); // 1ad047805bbf11eaa550ab02c68e3a5b
console.log(myWallet instanceof Wallet); // true
```
这种做法也实现了私有属性，解决了shape instanceof Shape 为 false 的问题。  
这种做法的缺点是兼容性较差，较为消耗性能
<br/>

## 4. 通过 '#'
```bash
class Point {
  #x;
  #y;

  constructor(x, y) {
    this.#x = x;
    this.#y = y;
  }

  equals(point) {
    return this.#x === point.#x && this.#y === point.#y;
  }
}
```
'#'是私有属性申明，当前还处于提案阶段，要经过Babel等编译器编译后才能使用  
希望这项提案能尽快通过，毕竟谁也不想为实现私有属性而写着么一大堆麻烦的东西。
<br/>

## 参考
《你不知道的JavaScript》
