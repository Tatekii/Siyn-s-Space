## 定义
**泛型（Generics）** 是 TypeScript 提供的一种 **类型参数化** 的机制，它允许我们编写**可复用的、类型安全的代码**，而不必预先指定具体的类型。

```ts
function identity<T>(value: T): T {
  return value;
}
const result = identity<string>("Hello"); // result 类型自动推导为 string
```

## 使用
### 函数中
```ts
function swap<T, U>(a: T, b: U): [T, U] {
  return [a, b];
}
const res = swap(1, "hello"); // res: [number, string]
```

### 接口中
```ts
interface Box<T> {
  value: T;
}
const stringBox: Box<string> = { value: "Hello" };
const numberBox: Box<number> = { value: 123 };
```

### 类中
```ts
class Stack<T> {
  private items: T[] = [];

  push(item: T) {
    this.items.push(item);
  }

  pop(): T | undefined {
    return this.items.pop();
  }
}

const numberStack = new Stack<number>();
numberStack.push(1);
console.log(numberStack.pop()); // 1
```

### 约束
```ts
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(arg: T): number {
  return arg.length;
}

console.log(logLength("Hello")); // ✅ 字符串有 length
console.log(logLength([1, 2, 3])); // ✅ 数组有 length
// console.log(logLength(123)); // ❌ 报错：数字没有 length
```

### 默认类型
```ts
function createArray<T = string>(length: number, value: T): T[] {
  return new Array(length).fill(value);
}
const arr1 = createArray(3, "A"); // 推导为 string[]
const arr2 = createArray<number>(3, 5); // number[]
```

### keyof
```ts
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

const person = { name: "Alice", age: 25 };
console.log(getProperty(person, "name")); // ✅ Alice
// console.log(getProperty(person, "gender")); // ❌ 报错：gender 不是 person 的 key
```
