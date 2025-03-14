## 定义
确保符合某个类型

## 作用
```ts
const user = {
  name: "Alice",
  age: 25,
} satisfies { name: string; age: number };

// `user` 仍然是 { name: "Alice"; age: 25 }
```

## 和as区别
```ts
// `as` 只是告诉 TypeScript："相信我，它是这个类型"
const num = "123" as number; // ❌ 不安全

// `satisfies` 确保 `user` 符合类型，但不会丢失信息
const user2 = {
  name: "Alice",
  age: 25,
} satisfies { name: string };
console.log(user2.age); // ✅ 仍然保留 age 信息
```