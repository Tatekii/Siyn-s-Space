## 定义

## 作用
- 元祖类型扩展范型
```typescript
	type ExtendTuple<T extends unknown[]> = [string,...T,number]

	type e1 = ExtendTuple<boolean>
	// [string,boolean,number]
	type e2 = ExtendTuple<[number,string]>
	// [string,number,string,number]
```