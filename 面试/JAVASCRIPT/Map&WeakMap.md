# `Map`
- 键能为任何类型
- 键为强引用
## 和Object的区别
- Object键只能为基本类型
- Object类型的数据会访问到原型上的键
	- `for...in`遍历所有可枚举的字符串属性
	- `Object.keys()`遍历可枚举的自有字符串键属性
	- `Object.getOwnPropertyNames`遍历包括不可枚举的自有字符串键属性
	- `Object.getOwnPropertySymbols遍历包括不可枚举的自有Symbol键属性
- Object没有迭代器
	- 无法`for...of`
- Object没有长度属性
- Object频繁插入删除性能没有Map好
### 注意
- 使用类object的操作方法不会报错但数据不会插入到Map中
	```javascript
	const wrongMap = new Map();
	wrongMap["bla"] = "blaa";
	wrongMap["bla2"] = "blaaa2";
	
	console.log(wrongMap); // Map { bla: 'blaa', bla2: 'blaaa2' }

	wrongMap.has("bla"); // false
	wrongMap.delete("bla"); // false
	```
## 构造函数
```javascript
new Map(iterator)
// 使用常规的 Map 构造函数可以将一个二维的键值对数组转换成一个 Map 对象
```
### 静态方法
- Map.groupBy :() => any
实例方法/属性
- Map.prototype.size
- Map.prototype.set: (key, value) => this
- Map.prototype.get: (key) => any
- Map.prototype.has: (key) => boolean
- Map.prototype.delete: (key) => boolean
- Map.prototype.clear: ()=> undefined
- Map.prototype.forEach: ((value, key, map)=>undefined, thisArg) => undefined
#### 返回迭代器
- Map.prototype.keys : () =>  Iterator
- Map.prototype.values: () => Iterator
- Map.prototype.entries: ()=> Iterator

# `WeakMap`
- 键只能为对象或**非全局Symbol**
- 键为弱引用，[[V8引擎#垃圾回收]]
- 不允许枚举，没有迭代器