在 TypeScript 中，as const 是 **类型断言**，它的作用是将一个值转换为 **字面量类型（literal type）**，并且 **将其变为只读（readonly）**。这样，TypeScript 在推导类型时不会将其视为更广泛的类型，而是精确地保留其字面量值。

### Object as const
```typescript
const obj1 = { name: "Alice", age: 25 }; 
// obj1 的类型被推导为 { name: string; age: number }

const obj2 = { name: "Alice", age: 25 } as const; 
// obj2 的类型是 { readonly name: "Alice"; readonly age: 25 }
```

### Array as const
```javascript

const arr1 = [1, 2, 3]; 
// arr1 的类型是 number[]，元素可以被修改

const arr2 = [1, 2, 3] as const; 
// arr2 的类型是 readonly [1, 2, 3]

```

### as const 后联合类型
```javascript
const colors = ["red", "green", "blue"] as const;
type Color = typeof colors[number]; 
// Color 的类型为 "red" | "green" | "blue"
```

### as const 严格枚举
```javascript
const STATUS = {
  SUCCESS: "success",
  FAILURE: "failure",
} as const;

type StatusType = typeof STATUS[keyof typeof STATUS];
// StatusType 变成 "success" | "failure"
```