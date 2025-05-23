## 定义
- 只能在[[extends]]中使用
- 在条件类型中，用于提取类型
## 使用
- 处于协变位置时，推断出联合类型
	```typescript
	type Foo<T> = T extends { a: infer U; b: infer U } ? U : never
	type T10 = Foo<{ a: string; b: string }> // string
	type T11 = Foo<{ a: string; b: number }> // string | number
	```
- 处于逆变(函数参数)位置时，推断出交叉类型
```typescript
	// 处于函数参数位置
	type Bar<T> = T extends { a: (x: infer U) => void; b: (x: infer U) => void } ? U : never

	type T20 = Bar<{ a: (x: string) => void; b: (x: string) => void }> // string

	type T21 = Bar<{ a: (x: string) => void; b: (x: number) => void }> // string & number => never

	type T22 = Bar<{ a: (x: {a:string}) => void; b: (x: {b:number}) => void }> // {a: string;} & {b: number;}

```

### 实际应用
#### UnwrapPromise
提取Promise的结果类型
```ts
type UnwrapPromise<T> = T extends Promise<infer R> ? R : T;

type A = UnwrapPromise<Promise<string>>; // string
type B = UnwrapPromise<Promise<number>>; // number
type C = UnwrapPromise<string>;          // string
```