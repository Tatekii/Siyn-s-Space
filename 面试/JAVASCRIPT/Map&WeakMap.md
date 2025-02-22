# `Map`
- 保存键值对
- 键能为任何类型
- 记住键的原始插入顺序
## 和Object的区别
- Object键只能为`String`,`Symbol`
-  Map 不会把键值对存储为自身的属性，而是使用**内部数据结构**（如哈希表），可以杜绝与原型上的属性相互影响
	```javascript
	const map = new Map();
	map.set("toString", "Hello");
	
	console.log(map.get("toString")); // "Hello"
	
	console.log(map.toString); // [Function: toString]，不会受影响
	
	map.toString === Map.prototype.toString // true
	```
- Object没有迭代器
	- 无法`for...of`
	- `for...in`遍历所有可枚举的字符串属性
	- `Object.keys()`遍历可枚举的自有字符串键属性
	- `Object.getOwnPropertyNames`遍历包括不可枚举的自有字符串键属性
	- `Object.getOwnPropertySymbols`遍历包括不可枚举的自有Symbol键属性
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
- 键不允许枚举，不可计数
## 构造函数
```javascript
new WeakMap(iterable)
// 一个数组者其他实现了[Symbol.iterator]方法的可迭代对象
```
### 实例方法
- WeakMap.prototype.delete : () => true
- Map.prototype.get: (key) => any
- Map.prototype.has: (key) => boolean
- Map.prototype.set: (key, value) => this
## 用途
模拟私有成员，将似有属性挂在对象身外。
```javascript
let Thing;

{
  const privateScope = new WeakMap();
  let counter = 0;

  Thing = function () {
    this.someProperty = "foo";

    privateScope.set(this, {
      hidden: ++counter,
    });
  };

  Thing.prototype.showPublic = function () {
    return this.someProperty;
  };

  Thing.prototype.showPrivate = function () {
    return privateScope.get(this).hidden;
  };
}

console.log(typeof privateScope);
// "undefined"

const thing = new Thing();

console.log(thing);
// Thing {someProperty: "foo"}

thing.showPublic();
// "foo"

thing.showPrivate();
// 1
```

# 使用场景
- vue3源码中订阅依赖管理部份使用Map+WeakMap组合关联组件对象与其依赖
```javascript
// /packages/reactivity/src/dep.ts
// The main WeakMap that stores {target -> key -> dep} connections.
// Conceptually, it's easier to think of a dependency as a Dep class
// which maintains a Set of subscribers, but we simply store them as
// raw Maps to reduce memory overhead.

type KeyToDepMap = Map<any, Dep>
export const targetMap: WeakMap<object, KeyToDepMap> = new WeakMap()


```
