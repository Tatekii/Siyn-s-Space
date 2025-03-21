Webpack
模块化打包工具

## 工作原理

1. 从入口 js 文件出发，通过各个模块的`require`或者`import`，解析出一棵包含所有使用到资源的依赖树
2. 递归这颗，对不同的资源类型根据`rules`使用不同的的`loader`加载
3. 各个模块加载的结果会作为一个个自执行函数放入`bundle`中，以拥有独立的作用域
4. 并且模块的加载存在缓存机制，重复加载的模块将直接从缓存对象中读取
5. 在打包过程中特定时机广播出特定钩子，`plugin`在对应的时机自动执行

## Hooks
[https://webpack.js.org/api/compiler-hooks/](https://webpack.js.org/api/compiler-hooks/)
- compiler.hooks：全局生命周期，如 done、emit
- compilation.hooks：单次编译，如 buildModule、seal

#### Loader
```javascript

// webpack.config.js

{
	module: {
		rules: [
			{
				test: /\.md$/i,
				use: ['html-loader',"./md-loader.js"]
			},
		],
	},
}

```


```javascript

// markdown-loader

const { parse } = require("marked");

module.exports = (source) => {

	return parse(source); // 交给下一个loader导出
	
	return `module.exports = ${parse(source)}`; // 这是最后的一个loader

};

```

  

#### Plugin
```javascript

// webpack.config.js

{
	plugins: [new CleanWebpackPlugin()],
}

```

  

#### Usage
- html-webpack-plugin
- 魔法字符串
```javascript

plugins: {

new HtmlWebpackPlugin({

title: "my title",

});

}

```

- webpack-dev-server
	- HMR 热更新
	- devServer:{hotOnly:true} 只替换不自动刷新
```javascript

// js

import myModule from "./myModule.js";

module.hot.accept("./myModule", () => {

// 回调函数中是新的状态

//...手动处理热替换逻辑

});

  

// pic

import pic1 from "./pic1";

module.hot.accept("./pic1", () => {

// 更换图片src

});

```

- cheap-module-eval-sourceMap
- webpack-merge
```javascript

const merge = require('webpack-merge')

const common = require('./webpack.common.js')

module.exports = merge(common,{

mode:"development",

plugins:[...],

module:{...}

})

```

- DefinePlugin

```javascript

plugins: [

new webpack.DefinePlugin({

API_ROOT: JSON.stringify("http:www.example.com/api"),

}),

];

```

  

#### 优化optimization

```javascript

optimization:{

// 摇树

usedExports:true,

// 作用域提升，尽量将模块合并到一个函数中

concatenateModules:true,

// 压缩

minimize:true,

// 检查package.json中声明代码有无副作用

sideEffects:true，

// 提取公共模块

splitChunks:true

},

plugins:{

// 多入口打包

new HtmlWebpackPlugin({

title:'Home',

template:'./src/index.html',

filename:'index.html',

chunks:['index']

}),

new HtmlWebpackPlugin({

title:'About',

template:'./src/about.html',

filename:'about.html',

chunks:['about']

})

}

```

```javascript

//<!-- package.json -->

{

sideEffects: ["/src/*.js", "/src/**/*.css"];

}

```
- 动态导入
	import 资源返回一个 promise
- 魔法注释
- MiniCssExtractPlugin 提取 css
```javascript

module:{

rules:[

{

test:/\.css$/,

use:[

MiniCssExtractPlugin.loader,

//'style-loader'用来添加style标签

'css-loader'

]

}

]

},

plugins:[

new MiniCssExtractPlugin()

]

// js，css代码压缩

optimization:{

minimizer:{ // 手动指定压缩选项

new OptimizeCssAssetsWebpackPlugin()

new TerserWebpackPlugin()

}

}

  

```

- hash
	- `[hash]`项目级别
	- `[chunkhash]`chunk 级别，同一个 chunk 到文件 hash 都相同
	- `[contenthash]`文件级别，根据内容生成
	- `[hash:8]`手动指定 hash 长度
	
	  
