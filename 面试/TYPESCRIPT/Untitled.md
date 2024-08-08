- `keyof`
	- 访问类型上的公共属性key
- `&`
	- 获取两个类型中的并集，如果在两个类型中有同key但类型不同的属性，该key类型为never
- `extends`
	- 继承接口`interface`,继承多个用逗号隔开
	- 用作条件类型做判断
		- 如果extends前面的类型为泛型，且泛型为联合类型，会分别判断
		```javascript
		type res<T> = T extends 'x' ? 1 : 2

		type res<'x'|'y'> // 1|2

		// 使用[]可以组织分发
		type res<T> = [T] extends 'x' ? 1 : 2

		type res<'x'|'y'> // 2
	   ```