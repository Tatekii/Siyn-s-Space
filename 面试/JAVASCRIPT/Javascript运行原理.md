## `[[Scope]]`
js中的每个函数都有一个内置属性scope，决定了函数可访问哪些变量。
- `[[scope]]`在函数被定义时就生成
- 存储了函数声明位置词法环境的引用（指针）

## 作用域链`Scope Chain`
>自由变量：当前作用域没有定义的变量，这成为`自由变量`（既不是局部变量也不是函数参数）

作用域链决定了变量查找的规则：
1. 查看当前作用域
2. 往上依次访问上级作用域
3. 在全局作用域后终止

## 全局对象 Global Object(`GO`)
在任何执行上下文创建之前，在[[堆]]内存中创建全局对象，包含以下属性：
- 内置对象：`Math`,`Date`,`String`,`parsInt`等。
- 宿主定义的属性：相当于全局对象本身的属性，对于浏览器来说就是`window`对象本身。
该对象对于所有作用域(`[[scope]]`)均可访问。


## 🪣执行环境栈 ECStack - execution context stack(`ESC`)
javascript 引擎执行代码的栈结构。

## 执行上下文 Execution Context(`EC`)
javascript 代码执行的环境，包含执行所需的变量和属性👇：

### 变量环境Variable Environment(`VE`)
VE是词法环境LE的一部份。
作用：
- 存储*var，函数声明*
- 处理变量提升
⚠️历史包袱：
	ES5和之前版本，只有var和function两种声明，所以之前也只有`VE`

### 词法环境Lexical Environment(`LE`)
每个js执行上下文都有其词法环境，包括：
1. 环境记录**Environment Record**: *存储let/const*，函数表达式，箭头函数声明.
2. 外部词法环境引用**Outer Lexical Environment Reference**: 指向外部词法环境的指针。
作用：
- 🔥负责处理作用域与闭包。
- 暂时性死区（**Temporal Dead Zone**TDZ）
注意：
- ⚠️词法环境在书写代码时就确定。
- ⚠️代替了ES5之前的VO对象。

### 绑定Banding
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
1. 全局词法环境`GLE`
	1. 全局变量环境`GVE`
2. `this`
	• In browsers → window
	• In Node.js → global
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