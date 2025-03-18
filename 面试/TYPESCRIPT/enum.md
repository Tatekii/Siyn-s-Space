# **TypeScript中的枚举（Enum）类型**

**枚举（Enum）** 是 TypeScript 提供的一种特殊数据类型，用于定义一组 **命名的常量**，使代码更具可读性和可维护性。

## 用法
### 默认行为
**默认情况下**，枚举成员从 0 开始递增（Up = 0, Down = 1, Left = 2, Right = 3）。
```ts
enum Direction {
  Up,
  Down,
  Left,
  Right
}

console.log(Direction.Up);   // 0
console.log(Direction.Down); // 1
console.log(Direction.Left); // 2
console.log(Direction.Right);// 3
```

### 数字枚举
数字枚举可以手动指定起始值，后续成员会递增：
```ts
enum Status {
  Success = 200,
  Continue
}

console.log(Status.Continue); // 201
```
可使用双向映射
```ts
console.log(Status[200]) // "Success"
```

### 字符串枚举
字符串枚举需要为每个成员手动赋值：
```ts
enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE"
}

console.log(Color.Red);  // "RED"
console.log(Color.Blue); // "BLUE"
```
**不能反向查找**（即 Color["RED"] 不可用）。

### 常量枚举
如果不需要对象映射，而只是使用常量值，可以使用 const enum 来优化性能：
```ts
const enum Role {
  User = 1,
  Admin = 2
}

console.log(Role.User); // 1
console.log(Role.Admin);// 2
```
**不能反向查找**（即 Color["RED"] 不可用）。

### 异构枚举
不推荐使用，不利于类型推断。

### 和[as const](类型操作/as%20const.md)的区别
在 TypeScript 4.5+ 之后，推荐使用 as const 代替 enum：
```ts
const Colors = {
  Red: "RED",
  Green: "GREEN",
  Blue: "BLUE"
} as const;

console.log(Colors.Red);  // "RED"
```

**优点**：

- **不会生成额外的 JavaScript 代码**。
- **更好的类型推断**，可与 typeof 结合使用：
	```ts
	type ColorType = typeof Colors[keyof typeof Colors]; // "RED" | "GREEN" | "BLUE"
	```