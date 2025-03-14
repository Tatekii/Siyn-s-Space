## 定义
表示类的继承（继承另一个类/接口）。用于 **限制泛型的范围** 或 **条件类型判断**。

## 作用
- 继承接口`interface`，多个用逗号隔开
- 用作条件类型做约束
	- 如果extends前面的类型为泛型，且泛型为联合类型，会分别判断
```javascript
type res<T> = T extends 'x' ? 1 : 2

type res<'x'|'y'> // 1|2

// 使用[]可以组织分发
type res<T> = [T] extends 'x' ? 1 : 2

type res<'x'|'y'> // 2
```
