## 词法环境**Lexical Environment**
每个js执行上下文都有其词法环境，包括：
1. 环境记录**Environment Record**: 存储所有变量和函数声明.
2. 外部词法环境引用**Outer Lexical Environment Reference**: 指向外部词法环境的指针。
🔥词法环境在代码编写时就已经确定🔥。


## `[[Scope]]`
js中的每个函数都有一个内置属性scope，决定了函数可访问哪些变量。
- `[[scope]]`在函数被定义时就生成
- 存储了函数声明位置词法环境的引用（指针）

## 作用域链Scope Chain
>自由变量：
作用域链决定了变量查找的规则：
1. 查看当前作用域
2. 往上依次访问上级作用域
3. 在全局作用域后终止

## 全局对象 Global Object(`GO`)
在任何执行上下文创建之前，在堆内存中创建全局对象，包含以下属性：
- 内置对象：`Math`,`Date`,`String`,`parsInt`等。
- 宿主定义的属性：相当于全局对象本身的属性，对于浏览器来说就是`window`对象本身。
该对象对于所有作用域(`[[scope]]`)均可访问。

## 全局执行上下文Global Execution Context(GEC))

## 全局词法环境**Global Lexical Environment (GLE)**


## 执行环境栈 ECStack - execution context stack(`ESC`)



## 执行上下文 Execution Context(`EC`)
javascript 引擎内部