# **TypeScript中的映射类型（Mapped Types）**

映射类型（Mapped Types）是 TypeScript 提供的一种强大特性，它允许你基于已有类型 **动态创建新类型**。这可以用于修改属性的可选性、只读性，或者改变类型本身。

```ts
type 新类型 = { [Key in 旧类型]: 修改后的类型 }
```

## 用法
### 修改类型
```ts
type User = {
  id: number;
  name: string;
  age: number;
};

type StringifiedUser = { [K in keyof User]: string };

const user: StringifiedUser = {
  id: "123", // 原本是 number，现在变成 string
  name: "John",
  age: "30",
};
```

### 属性可选
`Partial<K>`
```ts
type PartialUser = { [K in keyof User]?: User[K] };

const user: PartialUser = {
  name: "Alice", // age 和 id 可省略
};
```