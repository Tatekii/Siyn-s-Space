作用域决定变量在哪，执行上下文决定运行规则。

# 🪣执行环境栈 ECStack - execution context stack(`ESC`)
javascript ✈️引擎执行代码的栈结构。

# 执行上下文 Execution Context(`EC`)
[https://262.ecma-international.org/5.1/#sec-10.3](https://262.ecma-international.org/5.1/#sec-10.3)
```json
- Execution Context
	- Lexical Envirnoment
		- Enviroment Record
		- Outer Reference
	- Variable Enviroment
		- Enviroment Record
	- ThisBinding
```

javascript 代码执行时所需所需的变量和属性👇，在代码执行阶段动态生成，其包括：

## 词法环境Lexical Environment(`LE`)
### 环境记录
存储当前作用域中可变变量绑定，包括`let`,`const`。
- 处理暂时性死区（**Temporal Dead Zone**TDZ）机制，let/const声明位置之前无法读取
- 全局作用域下的let/const声明不会加入[[#全局对象 Global Object(`GO`)|GO]]
- 最新ECMA版本 => `CreateImMutableBinding`

### 外部词法环境引用`Outer Reference`
指向外部词法环境的指针。
🔥作用域与闭包机制

注意：
- ⚠️词法环境在书写代码时就确定。
- ⚠️代替了ES5之前的VO对象。

## 变量环境Variable Environment(`VE`)
### 环境记录
- 存储当前作用域中可变变量绑定,包括`var`,`funciton`
- 处理变量提升(`Hoisting`)
- 全局作用域下的var和函数声明会加入[[#全局对象 Global Object(`GO`)|全局对象]]中
- `CreateMutableBinding`

## This绑定ThisBanding
- 函数声明: `function myFunc(){}`取决于函数如何被执行
- 箭头函数: 绑定在函数所书写的处上下文
- 对象方法: `obj.method()`绑定到`.`之前的对象
- 函数构造器: `new MyFunc()`绑定到`new`出来的对象

## 💡按照环境记录的维度解释
LE和VE都是环境记录，环境记录可以理解为一个抽象类，有两个子类：
- 对象环境记录`Object Environment Records`
	- 存储为对象的一个属性
	- 场景
		- 全局作用域下的`var`,`function`声明
		- `with`语句
- 声明环境记录`Declarative Environment Records`
	- 直接存储在内存中而非对象的属性
	- 场景
		- 函数作用域
		- 块级作用域
```javascript
const obj = { name: "Alice", age: 25 };

with (obj) {
    console.log(name); // ✅ "Alice" (Looks inside `obj`)
    console.log(age); // ✅ 25
}

{
    let blockVar = "Inside block";
    const blockConst = "Me too";
    function blockFunc() {}
    console.log(blockVar); // ✅ "Inside block"
}

console.log(window.blockVar); // ❌ undefined
console.log(window.blockFunc); // ❌ undefined
```


### 😵‍💫ECMAScript版本差异
- ES<5，只有var和function两种声明，所以之前也只有`VE`，老版本中环境记录也称为`Variable Object (VO)`
- ES<5，函数执行上下中的VO被称为`Activation Object (AO)`
- 👀`AO = VO + arguments`
```javascript
function example(x) {
  console.log(y);  // undefined
  var y = 20;
  function inner() {
    return "Hello";
  }
}

example(5);


AO = {
  arguments: { 0: 5, length: 1 },  // Arguments object created
  x: 5,       // Function parameter stored in AO
  y: undefined,   // 'var' is hoisted but not assigned
  inner: function () { return "Hello"; }  // Function is fully ho
```



## 全局执行上下文Global Execution Context(GEC)
代码开始执行时，全局代码块构建为全局执行山下文压入执行上下文栈中。包含：

2. 全局词法环境`GLE`
	1. 全局变量环境`GVE`
3. `this`绑定到[[#全局对象 Global Object(`GO`)]]
	- In browsers → window
	- In Node.js → global
4. 外部引用: null
```javascript
var x = 10;
let y = 20;
const z = 30;

function foo() {
  console.log("Inside foo");
}

// ==>

GlobalExecutionContext = {
  this: window,  // In browsers
  LexicalEnvironment: {
    EnvironmentRecord: {
      VariableEnvironment: {
        x: undefined,  // `var` is hoisted but not initialized
        foo: function() { console.log("Inside foo"); } // Function is hoisted
      },
      LexicalEnvironment: {
        y: uninitialized, // `let` in Temporal Dead Zone (TDZ)
        z: uninitialized  // `const` in Temporal Dead Zone (TDZ)
      }
    },
    Outer: null  // Global has no parent scope
  }
};
```
### 全局对象 Global Object(`GO`)
在任何执行上下文创建之前，在[[堆]]内存中创建全局对象，包含以下属性：
- 内置对象：`Math`,`Date`,`String`,`parsInt`等。
- 宿主定义的属性：相当于全局对象本身的属性，对于浏览器来说就是`window`对象本身。
- ⚠️：**全局上下文中的var声明和function声明也会存储在GO中**。
该对象对于所有作用域(`[[scope]]`)均可访问。


## 函数执行上下文**Function Execution Context (FEC)**
- 函数执行时创建
- 略
## eval执行上下文**Eval Execution Context(EEC)**
- `eval()`执行时创建

# 作用域
作用域决定了变量和函数在代码中的可访问性，在编辑阶段就已经确定（书写时）。
- 全局作用域Global Scope
	- 全局声明的变量
	- 指向[[#执行上下文 Execution Context(`EC`)#全局执行上下文Global Execution Context(GEC)|全局执行上下文]]
- 函数作用域Function Scope
	- 函数内部声明的变量
	- 对外部环境的引用
- 块级作用域Block Scope
	- let /const声明的作用域在代码块`{}`中
- 模块作用域Module Scope
	- 各个ESModule中的作用域独立


## `[[Enviroment]]`
每个执行上下文具有一个内置属性`[[Enviroment]]`,指向当前的词法环境[[#词法环境Lexical Environment(`LE`)|LE]]。
注意：
- ⚠️`new`操作函数的`[[Environment]]`指向全局对象,所以只能显式传递参数


## `[[Scope]]`
js中的每个函数都有一个内置属性scope，决定了函数可访问哪些变量。
- `[[scope]]`在函数被定义时就生成
- 存储了函数声明位置词法环境的引用（指针）

### 作用域链`Scope Chain`
作用域链决定了变量查找的规则：
5. 查看当前作用域
6. 往上依次访问上级作用域
7. 到全局作用域后终止

随着程序的执行，会将当前的活动对象链接到`[scope chain]`的最前端。
```json
[[Scope]]:[CUR,...,GO]
```

# 闭包
> 闭包的概念最早出现在60年代，Schema，一个存储了函数和关联环境的结构体。
- 函数与其`词法环境`共同构成 _闭包（closure）_
	- 内层函数进行自由变量查找时可顺着作用域链访问外部的作用域
	- 也就是可以访问外部函数的词法环境LE，也就可以查找变量
- js中的函数声明时天然闭包

## ⭐️自由变量
当前作用域没有定义的变量，这成为`自由变量`（既不是局部变量也不是函数参数），自由变量的值会在捕捉闭包（作用域链）中查找的过程中确定。

## 闭包的用途
- 实现数据(变量和方法)私有化并缓存
```javascript
// 此处存在内存泄漏，increment为全局对象
function addCounter() 
	let counter = 0; // 执行结束后不会被清除
	
	const myFunction = function () {
	
		counter = counter + 1; // myFunction函数可以读取add函数内部的变量

		return counter;
	};
	
	return myFunction;
}

  

const increment = addCounter();

// addCounter函数执行完后出栈
// 但是其环境记录还存在内存中 -> counter

const c1 = increment();

const c2 = increment();

const c3 = increment();

console.log("increment:", c1, c2, c3); // 1,2,3

```

- 函数柯里化(变量收集和延迟调用)
```javascript

function curry(func,...args1){

const fnLen = func.length

const argsArr = [...args1]

function _curry(...args2){

argsArr = [...argsArr,...args2]

return argsArr.length >= fnLen ? func.apply(func,argsArr) : _curry

}

return _curry

}

  

// 传世经典累加函数add

function add(...args1){

let res = [...args1].reduce((a,b)=>a+b)

function innerAdd(...args2){

res = [...res,...args2].reduce((a,b)=>a+b)

return innerAdd

}

innerAdd.toString = () => res

// 打印方法会调用toString

return innerAdd

}

```


## 面试题

- 老生常谈的for循环setTimeout打印问题

```javascript

for (var i = 1; i < 7; i++) {

setTimeout(function () {

console.log(i)

}, 1000 * i)

}

// 6 6 6 6 6 6

// 创建的6个setTimeout闭包共享一个词法作用域

  

**闭包只能取得包含函数中任何变量赋值最后一个值** // 6

  
  

for (var i = 1; i < 7; i++) {

((j) => {

setTimeout(function(){

console.log(j)

}, 1000 * j)

})(i)

}

// 1 2 3 4 5 6

// 6个setTimeout闭包有自己独立的词法环境

// 闭包读取到不同的i值

  
  

for(var i = 1 ;i<7;i++){

setTimeout(function(j){

console.log(j);

},1000*i,i)

}

// setTimeout 可以接受一个参数当函数调用时传入

  

for(let i = 1 ;i<7;i++){

setTimeout(function(){

console.log(i);

},1000*i)

}

```

-

```javascript

function fn(num2) {

var num = 15;

function abc() { // 函数作为返回值

num++;

return(num + num2);

}

return abc;

}

var aa = fn(20); // 没有引用？？

aa(); //16+20

aa(); //17+20

fn(20)(); //36

fn(20)(); //36

```