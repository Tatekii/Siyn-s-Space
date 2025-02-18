作用域决定变量在哪，执行上下文决定运行规则。



# 🪣执行环境栈 ECStack - execution context stack(`ESC`)
javascript ✈️引擎执行代码的栈结构。

# 执行上下文 Execution Context(`EC`)
javascript 代码执行时所需所需的变量和属性👇，在代码执行阶段动态生成：

## 变量环境Variable Environment(`VE`)
VE是词法环境LE的一部份。
作用：
- 存储*var，函数声明*
- 处理变量提升
⚠️历史包袱：
	ES5和之前版本，只有var和function两种声明，所以之前也只有`VE`

## 词法环境Lexical Environment(`LE`)
每个js执行上下文都有其词法环境，包括：
4. 环境记录**Environment Record**: *存储let/const*，函数表达式，箭头函数声明.
5. 外部词法环境引用**Outer Lexical Environment Reference**: 指向外部词法环境的指针。
作用：
- 🔥负责处理作用域与闭包。
- 暂时性死区（**Temporal Dead Zone**TDZ）
注意：
- ⚠️词法环境在书写代码时就确定。
- ⚠️代替了ES5之前的VO对象。

## 绑定Banding
- 变量绑定
	变量声明 => 绑定到内存
	```javascript
	function outer() {
	  let a = 10;  // Variable 'a' is bound in the execution context of 'outer'
	  function inner() {
	    let b = 20;  // Variable 'b' is bound in the execution context of 'inner'
	    console.log(a + b);  // Accessing outer variable through scope chain binding
	  }
	  inner();
	}
	outer();
	```
- 词法绑定
	代码书写时就绑定词法环境
- ⭐️`this`绑定
	- 函数声明: `function myFunc(){}`取决于函数如何被执行
	- 箭头函数: 绑定在函数所书写的处上下文
	- 对象方法: `obj.method()`绑定到`.`之前的对象
	- 函数构造器: `new MyFunc()`绑定到`new`出来的对象
- 作用域链绑定


## 全局执行上下文Global Execution Context(GEC)
代码开始执行时，全局代码块构建为全局执行山下文压入执行上下文栈中。包含：
6. 全局词法环境`GLE`
	1. 全局变量环境`GVE`
7. `this`绑定到[[#全局对象 Global Object(`GO`)]]
	- In browsers → window
	- In Node.js → global
	
8. 外部引用: null
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
- 词法作用域Lexical Scope(Static Scope)
	- [[#作用域链`Scope Chain`]]

## `[[Scope]]`
js中的每个函数都有一个内置属性scope，决定了函数可访问哪些变量。
- `[[scope]]`在函数被定义时就生成
- 存储了函数声明位置词法环境的引用（指针）

### 自由变量
当前作用域没有定义的变量，这成为`自由变量`（既不是局部变量也不是函数参数）
### 作用域链`Scope Chain`
作用域链决定了变量查找的规则：
1. 查看当前作用域
2. 往上依次访问上级作用域
3. 到全局作用域后终止
随着程序的执行，会将当前的活动对象链接到`[scope chain]`的最前端。
```json
[[Scope]]:[GO,...,me]
```

# 闭包

- 函数与其`词法环境`共同构成 _闭包（closure）_
- `lexical environment`词法环境 = 代码内变量标识符与值之间的关联关系（环境记录`[[Environment]]`）+ 对外部词法环境的引用

- `new`操作函数的`[[Environment]]`指向全局对象,所以只能显式传递参数

- js中的函数声明时天然闭包(可以访问外部外部词法环境)

  

## 闭包的用途

  

- 实现数据(变量和方法)私有化

- 函数柯里化

  

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

  

## 效果

  

```javascript

function addCounter() {

let counter = 0; // 执行结束后不会被清除

const myFunction = function () {

counter = counter + 1; // myFunction函数可以读取add函数内部的变量

return counter;

};

return myFunction;

}

  

const increment = addCounter();

const c1 = increment();

const c2 = increment();

const c3 = increment();

console.log("increment:", c1, c2, c3);

// increment: 1 2 3

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