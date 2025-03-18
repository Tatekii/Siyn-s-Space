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
[`Partial`](类型工具.md#`Partial`)的实现
```ts
type PartialUser = { [K in keyof User]?: User[K] };

const user: PartialUser = {
  name: "Alice", // age 和 id 可省略
};
```

### 键名重映射
TypeScript 4.1+ 允许使用 as 关键字重映射键的名称
```ts
type UserWithPrefix = { [K in keyof User as `user_${K}`]: User[K] };

const user: UserWithPrefix = {
  user_id: 1,
  user_name: "Alice",
  user_age: 25,
};
```

### 属性过滤
通过 [extends](类型操作/extends.md) 进行条件判断，过滤出某些属性：
```ts
type NumericKeys<T> = { [K in keyof T as T[K] extends number ? K : never]: T[K] };

type NumericUser = NumericKeys<User>;

const user: NumericUser = {
  id: 1, // ✅ 只保留 number 类型的属性
  age: 25,
  // name: "Alice", // ❌ 报错，string 类型被过滤掉
};
```
[`Extract`](类型工具.md#`Extract`)的实现
```ts
type User = {
  id: number;
  name: string;
  age: number;
};

type ExtractProperty<T, K extends keyof T> = T[K];

type IDType = ExtractProperty<User, "id">; // number
```
### 联合类型映射
```ts
type UppercaseKeys<T extends string> = { [K in T]: Uppercase<K> };

type UppercaseRoles = UppercaseKeys<"admin" | "user" | "guest">;

// 结果等价于：
type UppercaseRoles = {
  admin: "ADMIN";
  user: "USER";
  guest: "GUEST";
};
```
