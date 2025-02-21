# `Proxy `
> 不兼容ie
**Proxy** 对象用于创建一个对象的代理，从而实现基本操作的拦截和自定义（如属性查找、赋值、枚举、函数调用等）。
```javascript
const p = new Proxy(target, handler)

handler.getPrototypeOf()

//Object.getPrototypeOf 方法的捕捉器。

handler.setPrototypeOf()

//Object.setPrototypeOf 方法的捕捉器。

handler.isExtensible()

//Object.isExtensible 方法的捕捉器。

handler.preventExtensions()

//Object.preventExtensions 方法的捕捉器。

handler.getOwnPropertyDescriptor()

//Object.getOwnPropertyDescriptor 方法的捕捉器。

handler.defineProperty()

//Object.defineProperty 方法的捕捉器。

handler.has()

//in 操作符的捕捉器。

handler.get(target,property,receiver)

//属性读取操作的捕捉器。

handler.set(target,property,value,receiver)

//属性设置操作的捕捉器。

handler.deleteProperty()

//delete 操作符的捕捉器。

handler.ownKeys()

//Object.getOwnPropertyNames 方法和 Object.getOwnPropertySymbols 方法的捕捉器。

handler.apply()

//函数调用操作的捕捉器。

handler.construct()
```
### `receiver`参数
- `handler的receiver`参数指向将会是handler实际被执行的上下文(`this`)
	```javascript
	
	const proxy = new Proxy({}, {
	// proxy 拦截了对 name 属性的赋值操作，因此 proxy 上的 set 处理器被触发。
	  set: function(target, prop, value, receiver) {
	
	    console.log(`set handler: Setting ${prop} to ${value}`);
	
	    console.log(`Receiver is:`, receiver); // receiver will be obj
	
	    target[prop] = value;
	
	    return true;
	
	  }
	
	});
	
	const obj = Object.create(proxy);  // obj 的原型是 proxy
	
	obj.name = "jen";  // 给 obj 设置 name 属性
	```
- [[#`Reflect`]]中的`receiver`将指定对象操作时的`this`指向
```javascript
const info = {
	_height:190
	get height(){
		return this._height + 'cm'
	}
}

const proxyInfo = new Proxy(info,{
	get(target,key,receiver){
		return target[key]// 只触发一次get handler 读取height
		return Reflect.get(target,key,receiver)// 会触发第二次get handler 读取_height
	}
})
```
## Object.defineProperty
> es5
`Object.defineProperty()` 允许精确地添加或修改对象上的属性。控制属性的可写、枚举和配置性的。
```javascript
Object.defineProperty(obj/* 对象*/, prop/* 属性*/, descriptor/*描述符*/){
//对象里目前存在的属性描述符有两种主要形式：数据描述符和存取描述符。
//数据描述符是一个具有值的属性，该值可以是可写的，也可以是不可写的。
//存取描述符是由 getter 函数和 setter 函数所描述的属性。
  

configurable: true,
//configurable 特性表示对象的属性是否可以被删除，以及除 value 和 writable 特性外的其他特性是否可以被修改。

enumerable: true,
//enumerable 定义了对象的属性是否可以在 for...in 循环和 Object.keys() 中被枚举。

value: true,

writable: true,
//当 writable 属性设置为 false 时，该属性被称为“不可写的”。它不能被重新赋值。

//

// 或者

//

get() { ... },

set(newValue) { ... },

}
```

# `Reflect`
操作对象的API
早起ECMA规范中没有考虑对对象本身的操作的API设计，就放在了`Object`上，而`Object`作为构造函数并不合适。
- 对象操作能返回布尔值
- `Object`未来可期能变得整洁
- 将命令式对象操作函数化，能抛出错误并返回执行结果
>	某些Object操作是命令式，比如name in obj和delete obj[name]，而Reflect.has(obj, name)和Reflect.deleteProperty(obj, name)让它们变成了函数行为。
- api与[[Proxy&Reflect]]中方法均对应，能做到不直接操作原对象
```javascript
const handlerWithReflect = {
  get: function (target, prop, receiver) {
    console.log("get handler with Reflect called");
    return Reflect.get(...arguments);
  },

  set: function (target, prop, value, receiver) {
    console.log("set handler with Reflect called");
    return Reflect.set(...arguments);
  },
};

const proxyWithReflect = new Proxy(target, handlerWithReflect);

```