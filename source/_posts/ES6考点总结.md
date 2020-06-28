---
title: ES6考点总结
date: 2020-06-27 18:54:05
tags:
---

## 箭头函数  
es6不再使用function()而使用()=>{}来定义  
  1. 用了箭头函数，this就不指向window，而是父级  
  2. 箭头函数不能用做构造函数，不能使用new命令  
  3. 箭头函数不能使用arguments对象，不可以使用yield命令，因此不能用作Generator函数

## 字符串模版  
字符串模版用(`${}`)标识，在es5中我们使用反斜杠(\\)来做多行字符串的拼接，在es6模版中，用反引号(``)就能解决问题
```js
let name = CC_CHEN
let age = 21
console.log(`${name}已经${age}岁了!`)
// CC_CHEN已经21岁了!
```

## Set数据结构
Set 数据结构，类似数组。所有的数据都是唯一的，没有重复的值。它本身是一个构造函数  
Set和Map的区别？  
Set 用于数据重组，Map 用于数据储存  
- Set 
  1. 成员不能重复 
  2. 只有键值没有键名，类似数组 
  3. 可以遍历，方法有 add, delete,has
- Map 
  1. 本质上是健值对的集合，类似集合 
  2. 可以遍历，可以跟各种数据格式转换  

下面Set结构，打印出的size值是多少
```js
let s = new Set()
s.add([1])
s.add([1])
console.log(s.size) // 2
```
因为两个数组[1]并不是同一个值，他们在内存中对应着不同的地址，因此，并不是相同的值都不能存储到Set结构中，所以size为2   

如何使用Set去重   
```js
let arr = [1,2,3,4,7,5,7]
let item = [...new Set(arr)]
console.log(item)  // [1,2,3,4,7,5]
```

## var let const的区别
- var 声明变量可以重复声明，而 let 不可以重复声明
- var 是不受限于块级的，而 let 是受限于块级
- var 会与 window 相映射(会挂一个属性)，而 let 不与 window 相映射
- var 可以在声明的上面访问变量，而 let 有暂存死区，在声明的上面访问变量会报错
- const 声明之后必须赋值，否则会报错
- const 定义不可变的量，改变了就会报错
- const 和 let 一样不会与 window 相映射、支持块级作用域、在声明的上面访问变量会报错

## Promise
Promise 构造函数是同步执行还是异步执行，那么 then 方法呢?  
promise 构造函数是同步执行的，then 方法是异步执行的，举例如下：
```js
const promise = new Promise((resolve, reject) => { 
  console.log(1);
  resolve();
  console.log(2);
})
promise.then(() => { 
  console.log(3);
})
console.log(4);
// 1 2 4 3
```
Promise 新建后立即执行，所以会先输出 1 2，而 Promise.then() 内部的代码在当次事件循环的结尾立刻执行，所以会继续输出 4，最后输出 3  

Promise 有几种状态，什么时候会进入 catch?  
- 三个状态:pending、fulfilled、reject 
- 两个过程:padding -> fulfilled、padding -> rejected 
- 当 pending 为 rejectd 时，会进入 catch

Promise中reject和catch在处理上有什么区别
- reject是用来抛出异常，catch是用来处理异常
- reject是Promise的方法，而catch是Promise的实例
- reject后的东西，一定会进入then中的第二个回调，如果then中没有写第二个回调，则进入catch
- 网络异常(比如断网)，会直接进入catch，而不会进入then的第二个回调

请使用class手写一个Promise
```js
class Promise {
  constructor(executer) {  //构造函数 constructor 里面是个执行器
    this.status = 'pending';//默认的状态 pending 
    this.value = undefined//成功的值默认 undefined 
    this.reason = undefined//失败的值默认 undefined 
    // 状态只有在 pending 时候才能改变

    let resolveFn = value => {
      //判断只有等待时才能 resolve 成功
      if (this.status == pending) {
        this.status = 'resolve';
        this.value = value;
      }
    }

    //判断只有等待时才能 reject 失败
    let rejectFn = reason => {
      if (this.status == pending) {
        this.status = 'reject';
        this.reason = reason;
      }
    }

    try {
      //把 resolve 和 reject 两个函数传给执行器executer
      executer(resolve, reject);
    } catch (e) {
      reject(e);//失败的话进 catch                 
    }
  }
  
  then(onFufilled, onReject) {
    //如果状态成功调用 onFufilled
    if (this.status = 'resolve') {
      onFufilled(this.value);
    }
    //如果状态失败调用 onReject
    if (this.status = 'reject') {
      onReject(this.reason);
    }
  }
}
```

## async、await
使用 async/await, 搭配 promise,可以通过编写形似同步的代码来处理异步流程, 提高代码的简洁性和可读性  
async 用于申明一个 function 是异步的，而 await 用于等待一个异步方法执行完成   

理解 async/await 以及对 Generator 的优势
- async await 是用来解决异步的，async 函数是  Generator 函数的语法糖
- 使用关键字 async 来表示，在函数内部使用 await 来表示异步
- async 函数返回一个 Promise 对象，可以使用 then 方法添加回调函数
- 当函数执行的时候，一旦遇到 await 就会先返回，等到 异步操作完成，再接着执行函数体内后面的语句   

async 较 Generator 的优势:
- 内置执行器。Generator 函数的执行必须依靠执行器，而 Aysnc 函数自带执行器，调用方式跟普通函数的 调用一样
- 更好的语义。async 和 await 相较于 * 和 yield 更加语义化
- 更广的适用性。yield 命令后面只能是 Thunk 函数或 Promise 对象，async 函数的 await 后面可以是 Promise 也可以是原始类型的值
(4)返回值是 Promise。async 函数返回的是 Promise 对象，比 Generator 函数返回的 Iterator 对象方便，可以直接使用 then() 方法进行调用

setTimeout、Promise、Async/Await 的区别  
- 事件循环中分为宏任务队列和微任务队列  
- 其中 setTimeout 的回调函数放到宏任务队列里，等到执行栈清空以后执行  
- promise.then 里的回调函数会放到相应宏任务的微任务队列里，等宏任务里面的同步代码执行完再执行            
- async 函数表示函数里面可能会有异步方法，await 后面跟一个表达式  
- async 方法执行时，遇到 await 会立即执行表达式，然后把表达式后面的代码放到微任务队列里，让出执行栈让同步代码先执行

## symbol
symbol 是一种基本数据类型。Symbol()函数会返回symbol类型的值，该类型具有静态属性和静态方法   
设计一个对象，键名的类型至少包含一个 symbol 类型， 并且实现遍历所有 key
```js
let name = Symbol('name')
let product = {
  [name]: "洗衣机",
  "price": 799
}
Reflect.ownKeys(product)
```

## forEach、for in、for of的区别
- forEach 更多的用来遍历数组
- for in 一般常用来遍历对象或 json
- for of 数组对象都可以遍历，遍历对象需要通过和 Object.keys()
- for in 循环出的是 key，for of 循环出的是 value
