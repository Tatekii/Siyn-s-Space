
# 迭代器
在 JavaScript 中，`Iterator` 对象是一种实现了迭代协议的对象，可以用来逐个访问一个集合中的每一项。`Iterator` 是 ES6（ECMAScript 2015）引入的一个重要概念，它使得对集合的遍历变得更加方便和一致。

### 迭代器对象
一个迭代器对象包含一个 `next` 方法，该方法返回一个形如 `{ value: any, done: boolean }` 的对象。`value` 是迭代器返回的值，`done` 是一个布尔值，表示迭代是否结束。

### 迭代协议

JavaScript 中的迭代协议由两个部分组成：

1. **可迭代协议（Iterable Protocol）**：
    
    - 一个对象实现了可迭代协议，就说明这个对象可以被迭代。对象需要实现一个名为 `@@iterator` 的方法，也就是对象上存在一个 `Symbol.iterator` 属性。
    - 这个方法无参数，返回一个 `Iterator` 对象。
2. **迭代器协议（Iterator Protocol）**：
    
    - 一个对象实现了迭代器协议，必须提供一个 `next` 方法。
    - `next` 方法返回一个对象，该对象包含两个属性：
        - `value`：迭代器返回的下一个值。
        - `done`：布尔值，表示迭代是否已经完成。


### 作用
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