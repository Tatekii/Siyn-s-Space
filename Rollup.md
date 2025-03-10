### Rollup

ESModule 模块打包器
- 自动摇树
- 打包 npm 模块能力(node_module/)
```javascript

import resolve from "rollup-plugin-node-resolve";

export default {

plugins: [resolve()],

};

```

- 支持 commonjs 模块

```javascript

import commonjs from "rollup-commonjs-plugin";

export default {

plugins: [commonjs()],

};

```

- 动态引入/代码分割

```javascript

output:{

// file:'dist/bundle.js',

// format:'iife'

dir:'dist',// 只能指定目录

format:'amd'// 不能使用iife格式

},

```

```javascript

// moduleA.js

import('./moduleB.js').then({m}=>{

console.log(m.default)

})

```

- 多入口打包

自动代码分割提取公共模块

```javascript

export default {

input: [

// 直接改成数组

],

};

```
