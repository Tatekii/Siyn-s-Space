# Promise对象
Promise 对象用于处理异步操作，它表示一个尚未完成且预计在未来完成的异步操作。

## 特性
- 有限状态机，状态只能改变一次
- 支持链式调用，不用将所有回调函数一开始就作为参数传入（避免回调地狱）
- 返回值穿透（onFulfilled 不是 function 时，上一步的执行结果 value 将继续向后传递）
- 错误冒泡，错误会一直往外冒泡到catch直至显示有未handle的异步错误

## 原型方法
### Promise.prototype.then()
- 为 Promise 实例添加状态改变时的回调函数
- then 方法的第一个参数是`resolved`状态的回调函数，第二个参数（可选）是`rejected`状态的回调函数
- then 方法返回的是一个新的 Promise 实例,可以 then 方法后面再调用另一个 then 方法

```javascript

getJSON("/post/1.json")

.then(function (post) {

	return getJSON(post.commentURL);

})

.then(

function funcA(comments) {

	console.log("resolved: ", comments);

},

function funcB(err) {

	console.log("rejected: ", err);

}

);

// 第一个then方法指定的回调函数，返回的是另一个Promise对象。这时，第二个then方法指定的回调函数，就会等待这个新的Promise对象状态发生变化。如果变为resolved，就调用funcA，如果状态变为rejected，就调用funcB。

```

### Promise.prototype.catch()
- 指定发生错误时的回调函数
- Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个`catch`语句捕获
```javascript

// 一般来说，不要在then方法里面定义 Reject 状态的回调函数（即then的第二个参数），总是使用catch方法。

// bad
promise.then(
	function (data) {
	// success
	},
	function (err) {
	// error
	}
);

  

// good
promise
.then(function (data) {
//cb
// success
})
.catch(function (err) {
// error
});

```
> 跟传统的`try/catch`代码块不同的是，如果没有使用`catch`方法指定错误处理的回调函数，Promise 对象抛出的错误不会传递到外层代码，即不会有任何反应
- catch 方法返回的还是一个 Promise 对象，因此后面还可以接着调用 then 方法

### Promise.prototype.finally()
- finally 方法用于指定不管 Promise 对象最后状态如何，都会执行的操作
- finally 方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是 fulfilled 还是 rejected。这表明，finally 方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。

## 静态方法
### Promise.all()
Promise.all 方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

`const p = Promise.all([p1, p2, p3]);`

  p 的状态由 p1、p2、p3 决定，分成两种情况。
1. 只有 p1、p2、p3 的状态都变成`fulfilled`，p 的状态才会变成`fulfilled`，此时 p1、p2、p3 的返回值组成一个数组，传递给 p 的回调函数。  
2. 只要 p1、p2、p3 之中有一个被`rejected`，p 的状态就变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给 p 的回调函数
> 如果作为参数的 Promise 实例，自己定义了 catch 方法，那么它一旦被 rejected，并不会触发 Promise.all()的 catch 方法

### Promise.race()
Promise.race 方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。

`const p = Promise.race([p1, p2, p3]);`
  
上面代码中，只要 p1、p2、p3 之中有一个实例率先改变状态，p 的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给 p 的回调函数。

```javascript

//example

const p = Promise.race([

fetch("/resource-that-may-take-a-while"),

new Promise(function (resolve, reject) {

setTimeout(() => reject(new Error("request timeout")), 5000);

}), // 5s内请求不resolve则抛出错误

]);

  

p.then(console.log).catch(console.error);

```

### promise.try()
不想区分，函数 f 是同步函数还是异步操作，但是想用 Promise 来处理它

```javascript

try {
	database.users.get({id: userId})
	.then(...)
	.catch(...) // promise错误
} catch (e) { // 同步错误
// ...
}

// try catch只能捕获同步错误
// 用promise.try改写

Promise.try(() => database.users.get({id: userId}))
.then(...)
.catch(...)

```

  

## 手写 Promise
- 测试通过 A+规范
```javascript

promises-aplus-tests

node promises-aplus-tests yourpeomise.js

  

// yourpromise.js

MyPromise.defer = MyPromise.deferred = function () {

let dfd = {};

dfd.promise = new MyPromise((resolve, reject) => {

dfd.resolve = resolve;

dfd.reject = reject;

});

return dfd;

};

module.exports = MyPromise

```
- resolvePromise
```javascript

const isComplex = (obj) => obj !== null && (typeof obj === "function" || typeof obj === "object");

// 判断then之后的状态

function resolvePromise(x, promise2, resolve, reject) {

if (x === promise2) {

reject(new TypeError("Chaining cycle detected for promise"));

}

if (isComplex(x)) {

let hasRes = false;

try {

const then = x.then;

// 如果x是promise，继续判断

if (typeof then === "function") {

then.call(

x,

(v) => {

if (hasRes) return;

hasRes = true;

resolvePromise(v, promise2, resolve, reject);

},

(r) => {

if (hasRes) return;

hasRes = true;

reject(r);

}

);

} else {

// 如果x是个对象

resolve(x);

}

} catch (e) {

if (hasRes) return;

hasRes = true;

reject(e);

}

} else {

resolve(x);

}

}

  

// 通用常量

const PENDING = Symbol('PENDING');

const FULFILLED = Symbol('FULFILLED');

const REJECTED = Symbol('REJECTED');

```
- function 版
```javascript

function MyPromise(executor) {

this.status = PENDING;

this.data = undefined;

this.onFulfilledCb = [];

this.onRejectedCd = [];

  

const resolve = (value) => {

setTimeout(() => {

// 放在下个宏任务等promise2都执行得出状态

if (this.status === PENDING) {

this.status = FULFILLED;

this.data = value;

this.onFulfilledCb.forEach((cb) => cb());

}

});

};

const reject = (reason) => {

setTimeout(() => {

// 放在下个宏任务等promise2都执行得出状态

if (this.status === PENDING) {

this.status = REJECTED;

this.data = reason;

this.onRejectedCd.forEach((cb) => cb());

}

});

};

  

try {

executor(resolve, reject);

} catch (e) {

reject(e);

}

}

  

MyPromise.prototype.then = function (onFulfill, onReject) {

onFulfill = typeof onFulfill === "function" ? onFulfill : (value) => value;

onReject =

typeof onReject === "function"

? onReject

: (err) => {

throw err;

};

let promise2 = new Promise((resolve, reject) => {

if (this.status === PENDING) {

// push进去的时候promise2还没有执行完

this.onFulfilledCb.push(() => {

try {

let x = onFulfill(this.data);

resolvePromise(x, promise2, resolve, reject);

} catch (e) {

reject(e);

}

});

this.onRejectedCd.push(() => {

try {

let x = onReject(this.data);

resolvePromise(x, promise2, resolve, reject);

} catch (e) {

reject(e);

}

});

// 由于new Promise构造函数逻辑

// 可以简写

} else if (this.status === FULFILLED) {

// 如果返回一个promise，依然需要判断他后续的状态,交给下一个then时去判断👆的代码

setTimeout(() => onFulfill(this.data));

// setTimeout(()=>{

// try{

// let x = onFulfill(this.data)

// resolvePromise(x,promise2,resolve,reject)

// }catch(e){

// reject(e)

// }

// })

} else if (this.status === REJECTED) {

setTimeout(() => onReject(this.data));

}

});

  

return promise2;

};

```

  

- class 版

  

```javascript

class MyPromise {

constructor(executor) {

try {

executor(this.resolve, this.reject)

} catch (err) {

this.reject(err)

}

}

// 状态

status = PENDING

// 返回成功结果或者失败结果

data = null

// 成功与失败的回调

fulfilledCd = []

rejectedCb = []

  

resolve = (value) => {

setTimeout(() => {

if (this.status === PENDING) {

this.data = value

this.status = FULFILLED

this.fulfilledCd.forEach((cb) => cb())

}

})

}

  

reject = (reason) => {

setTimeout(() => {

if (this.status === PENDING) {

this.data = reason

this.status = REJECTED

this.rejectedCb.forEach((cb) => cb())

}

})

}

  

then = (onResolve, onReject) => {

onResolve = typeof onResolve === "function" ? onResolve : (val) => val

onReject =

typeof onReject === "function"

? onReject

: (err) => {

throw err

}

  

// then 返回一个promise

const promise2 = new MyPromise((resolve, reject) => {

if (this.status === PENDING) {

this.fulfilledCd.push(() => {

try {

const x = onResolve(this.data)

resolvePromise(x, promise2, resolve, reject)

} catch (err) {

reject(err)

}

})

  

this.rejectedCb.push(() => {

try {

const x = onReject(this.data)

resolvePromise(x, promise2, resolve, reject)

} catch (err) {

reject(err)

}

})

} else if (this.status === FULFILLED) {

setTimeout(() => onResolve(this.data))

} else if ((this.status = REJECTED)) {

setTimeout(() => onReject(this.data))

}

})

  

return promise2

}

}

```

  

- API

  

```javascript

Promise.resolve = (value) => new Promise((resolve, reject) => resolve(value));

Promise.reject = (reason) => new Promise((resolve, reject) => reject(reason));

// catch

Promise.prototype.catch = (onReject) => {

return this.then(null, onReject);

};

// finally

Promise.prototype.finally = function (callback) {

return this.then(

(value) => {

Promise.resolve(callback()).then(() => value);

},

(reason) => {

Promise.resolve(callback()).then(() => {

throw reason;

});

}

);

};

// race

Promise.race = function (pArr) {

// 抛出第一个改变的状态

return new Promise((resolve, reject) => {

pArr.forEach((pItem) => pItem.then(resolve, reject));

});

};

// any

Promise.any = function (pArr) {

// 有resolve就resolve

// 都没有resolve则返回一个由所有err组成的AggregateError对象

return new Promise((resolve, reject) => {

if (!pArr.length) return reject(new AggregateError([]));

const res = [];

pArr.forEach((pItem, index) => {

pItem.then(

(v) => {

resolve(v);

},

(e) => {

res[index] = e;

}

);

});

reject(new AggregateError(res));

});

};

  

// all

Promise.all = function (pArr) {

// resolve全部value

// 有err就reject

return new Promise((resolve, reject) => {

if (!pArr.length) return resolve([]);

const res = [];

pArr.forEach((pItem, index) => {

pItem.then(

(v) => {

res[index] = v;

},

(e) => {

reject(e);

}

);

});

resolve(res);

});

};

  

// allSettled

Promise.allSettled = function (pArr) {

// 记录每个promise的状态

return new Promise((resolve, reject) => {

if (!pArr.length) return resolve([]);

const res = [];

pArr.forEach((pItem, index) => {

// if (pItem instanceof Promise) {

pItem.then(

(v) => {

res[index] = { status: "resolve", value: v };

},

(e) => {

res[index] = { status: "reject", reason: e };

}

);

// } else {

// res[index] = { status: "resolve", value: pItem };

// }

});

resolve(res);

});

};

```

## 面试题
- 同步函数 promise 化
```javascript
function promisify<T>(fn: (...args: any[]) => T): (...args: any[]) => Promise<T> {

	return (...args) => {
	
		return new Promise((resolve, reject) => {
		
		try {
		
			resolve(fn.apply(null, args))
		
		} catch (err) {
		
			reject(err)
		
		}
		
		})
	
	}

}
```
- promise的状态由另一个promise决定
```javascript

const p1 = new Promise(function (resolve, reject) {
// ...
});

  

const p2 = new Promise(function (resolve, reject) {
// ...
resolve(p1);
});

//p1的状态决定了p2的状态。如果p1的状态是pending，那么p2的回调函数就会等待p1的状态改变；如果p1的状态已经是resolved或者rejected，那么p2的回调函数将会立刻执行。

const p1 = new Promise(function (resolve, reject) {
	setTimeout(() => reject(new Error("3000s fail")), 3000);
});

  

const p2 = new Promise(function (resolve, reject) {
	setTimeout(() => resolve(p1), 1000);
});

  

p2.then((result) => console.log(result)).catch((error) => console.log(error));

// Error: fail

// 上面代码中，p1是一个 Promise，3 秒之后变为rejected。p2的状态在 1 秒之后改变，resolve方法返回的是p1。由于p2返回的是另一个 Promise，导致p2自己的状态无效了，由p1的状态决定p2的状态。所以，后面的then语句都变成针对后者（p1）。又过了 2 秒，p1变为rejected，导致触发catch方法指定的回调函数。

```

- 在then中打印Promise
```javascript

const p1 = new Promise((resolve,reject) => {

	setTimeout(()=>{
	
	resolve('resolve3')
	
	console.log('timer1')

})

resolve('resolve1')

resolve('resolve2')

}).then(res=>{

	console.log(res)
	
	setTimeout(()=>{
	
		console.log(p1)

	},1000)

  

}).finally(res=>{

	console.log(res)

})

  

// resolve1 状态只改变一次

// undefined finally没有接受参数

// timer1

// Promise {fulfilled} !!! ⭐️被then调用后，返回了新的Promise,但是没有return为undefined

```