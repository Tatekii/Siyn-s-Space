
# 异步迭代器对象
**`AsyncIterator`** 对象是符合[异步迭代器协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#%E5%BC%82%E6%AD%A5%E8%BF%AD%E4%BB%A3%E5%99%A8%E5%92%8C%E5%BC%82%E6%AD%A5%E5%8F%AF%E8%BF%AD%E4%BB%A3%E5%8D%8F%E8%AE%AE)的对象，其提供了 `next()` 方法用以返回一个兑现为迭代器结果对象的 promise。`AsyncIterator.prototype` 对象是一个隐藏的全局对象，所有内置的异步迭代器都继承自它。其提供了 [`@@asyncIterator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/AsyncIterator/@@asyncIterator) 方法，该方法返回异步迭代器对象本身，使异步迭代器也[异步可迭代](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#%E5%BC%82%E6%AD%A5%E8%BF%AD%E4%BB%A3%E5%99%A8%E5%92%8C%E5%BC%82%E6%AD%A5%E5%8F%AF%E8%BF%AD%E4%BB%A3%E5%8D%8F%E8%AE%AE)。

## 异步可迭代协议
当对象实现以下方法时，它会实现异步可迭代协议：

[`[Symbol.asyncIterator]`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol/asyncIterator)

## 异步迭代器协议
当对象实现以下方法时，它会实现异步迭代器协议：

[`next()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#next_2)

无参数或者接受一个参数的函数，并返回 promise。promise 兑现为一个对象，该对象符合 `IteratorResult` 接口，并且这些属性与同步迭代器有着相同的语义。

[`return(value)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#returnvalue_2) 可选

无参数或者接受一个参数的函数，并返回 promise。promise 兑现为一个对象，该对象符合 `IteratorResult` 接口，并且这些属性与同步迭代器有着相同的语义。

[`throw(exception)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#throwexception_2) 可选

无参数或者接受一个参数的函数，并返回 promise。promise 兑现为一个对象，该对象符合 `IteratorResult` 接口，并且这些属性与同步迭代器有着相同的语义。

> 目前只有异步生成器函数返回的`AsyncGenerator`对象内置了`AsyncIterator`对象。

## 使用
所有内置异步迭代器都可以异步迭代，因此可在 `for await...of` 循环中调用。