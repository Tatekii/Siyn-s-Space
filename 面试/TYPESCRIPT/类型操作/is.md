## 定义
类型谓词（Type Predicate）

## 作用
- 用于 **类型守卫（Type Guard）**，在 **运行时动态检查类型**。
	```ts
	function isString(value: unknown): value is string {
	  return typeof value === "string";
	}
	
	function print(value: unknown) {
	  if (isString(value)) {
	    console.log(value.toUpperCase()); // ✅ 这里 TypeScript 知道 value 是 string
	  }
	}
	```