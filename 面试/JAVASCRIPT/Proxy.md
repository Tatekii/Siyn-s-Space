# Proxy类
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
## Object.defineProperty
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