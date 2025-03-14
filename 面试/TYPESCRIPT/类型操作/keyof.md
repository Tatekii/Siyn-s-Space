## 定义
访问类型上的公共属性key，返回 **联合类型**。

```ts
type Person = { name: string; age: number };
type Keys = keyof Person; // "name" | "age"

const key: Keys = "name"; // ✅ 合法
// const key2: Keys = "gender"; // ❌ 报错，"gender" 不在 Person 里
```
