# 迭代器对象
在 JavaScript 中，`Iterator` 对象是一种实现了迭代协议的对象，可以用来逐个访问一个集合中的每一项。`Iterator` 是 ES6（ECMAScript 2015）引入的一个重要概念，它使得对集合的遍历变得更加方便和一致。

## 迭代协议
JavaScript 中的迭代协议由两个部分组成：

1. **可迭代协议（Iterable Protocol）**：
    
    - 一个对象实现了可迭代协议，就说明这个对象可以被迭代。
    - 对象需要实现一个名为 `@@iterator` 的方法，也就是对象上存在一个 `[Symbol.iterator]` 属性，可通过常量 `Symbol.iterator` 访问该属性：。
    - 这个方法无参数，返回一个符合迭代器协议的对象。

2. **迭代器协议（Iterator Protocol）**：
    
    - 一个对象实现了迭代器协议，必须提供一个 `next` 方法。
    - `next` 方法返回一个对象，该对象包含两个属性：
        - `value`：迭代器返回的下一个值。
        - `done`：布尔值，表示迭代是否已经完成。


## 作用
为javascript中的对象提供了迭代的接口，比如for...of就是调用了[Symbol.Iterator]方法获取了迭代器，再通过执行next迭代对象的值。
```javascript
const array = [1, 2, 3];

const iterator = array[Symbol.iterator]();

console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: undefined, done: true }

for (const value of array) {
  console.log(value);
}
// 输出：
// 1
// 2
// 3
```
其他例如...展开运算符，解构赋值，Array.From等均是调用了这个接口。

# 异步迭代器对象
**`AsyncIterator`** 对象是符合异步迭代器协议的对象，其提供了 `next()` 方法用以返回一个兑现为迭代器结果对象的 promise。`AsyncIterator.prototype` 对象是一个隐藏的全局对象，所有内置的异步迭代器都继承自它。其提供了 `@@asyncIterator`方法，该方法返回异步迭代器对象本身，使异步迭代器也异步可迭代。

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


```bash
[][Symbol.iterator]()

Array Iterator {}
  [[Prototype]]: Array Iterator 
    next: ƒ next()
    Symbol(Symbol.toStringTag): "Array Iterator"
    [[Prototype]]: Object
      Symbol(Symbol.iterator): ƒ [Symbol.iterator]()
      [[Prototype]]: Object
```


## 作用
所有内置异步迭代器都可以异步迭代，因此可在 `for await...of` 循环中调用。