# 总结

| **特性**    | interface                                       | type                                         |
| --------- | ----------------------------------------------- | -------------------------------------------- |
| **基本用法**  | interface Person { name: string; age: number; } | type Person = { name: string; age: number; } |
| **是否可扩展** | **支持继承**（extends 和声明合并）                         | **不可重复定义**，但可以使用 & 进行合并                      |
| **支持的类型** | 只能用于 **对象** 和 **类**                             | 可以表示 **任何类型**（对象、基本类型、联合类型、元组等）              |
| **性能**    | **编译器优化更好**，推荐用于对象类型                            | **灵活性更高**，但在大型项目中可能影响性能                      |
| **可用于类**  | ✅ 可以用于类的 implements                             | ❌ 不能用于 implements                            |
| 声明合并      | 支持                                              | 不支持                                          |

## 示例
```typescript
// interface 适用于对象
interface User {
  name: string;
  age: number;
}

// type 适用于基本类型、联合类型、交叉类型
type ID = string | number;
type Admin = User & { permissions: string[] };

// interface 可以扩展
interface Employee extends User {
  salary: number;
}

// type 可以组合多个类型
type Manager = Employee & { teamSize: number };

// interface 可合并
interface User {
  email: string; // 这里会自动合并
}

// type 不能合并，必须使用 `&`
type FullUser = User & { address: string };
```

## 编译优化
在 TypeScript 编译过程中，interface 会被转换成 **一个 VTable（虚表）**，也就是一个固定的结构，这种方式 **类似于 Java 和 C++ 里的类接口（Interface）**。

如果多个对象使用了相同的 interface，编译器不会重复创建类型，而是直接引用已创建的类型，从而加快编译速度。

type **无法声明合并，每次定义都会创建新的类型**，而且 type 允许更复杂的类型操作，比如 &（交叉类型）、|（联合类型），导致 **编译器需要额外的计算**，影响性能。

### 示例
```typescript
type A = { name: string };
type B = { age: number };
type C = A & B; // 交叉类型

const obj: C = { name: "Alice", age: 25 };
```
- **编译器在解析 C 时，无法直接复用 A 和 B，而是必须重新计算交叉类型**，这会影响编译性能。
- 当 type 进行复杂的 **类型合并** 时，编译器需要 **不断创建新类型**，无法像 interface 那样进行类型结构复用。