## 词法环境**Lexical Environment**


## 全局对象 Global Object(`GO`)
在任何执行上下文创建之前，在堆内存中创建全局对象，包含以下属性：
- 内置对象：`Math`,`Date`,`String`,`parsInt`等。
- 宿主定义的属性：相当于全局对象本身的属性，对于浏览器来说就是`window`对象本身。
该对象对于所有作用域(`[[scope]]`)均可访问。


## 执行环境栈 ECStack - execution context stack(`ESC`)

## 全局执行上下文Global Execution Context(GEC))

## 执行上下文 Execution Context(`EC`)
javascript 引擎内部