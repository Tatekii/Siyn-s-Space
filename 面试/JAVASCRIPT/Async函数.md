# AsyncFunction
**`async function`** 声明创建一个[绑定](https://developer.mozilla.org/zh-CN/docs/Glossary/Binding)到给定名称的新异步函数。函数体内允许使用 `await` 关键字，这使得我们可以更简洁地编写基于 promise 的异步代码，并且避免了显式地配置 promise 链的需要。

## 特性
```javascript
async function fun2 (){
	return 2;
}
// 等价于
function fun2 () {
	return Promise.resolve(2);
}



async function fun1 () {
	await 1;
}
// 等价于
function fun1 () {
	return Promise.resolve(1).then(() => undefined)
}

```

## 构造函数

[`AsyncFunction()`]
- 创建一个新的 `AsyncFunction` 对象
- 不能直接拿到，可通过`async function(){}.constructor`获取
