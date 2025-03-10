### Gulp

基于流的构建工具

默认任务都是异步的，需要手动调用结束任务  

```javascript

// gulpfile.js

exports.foo = (done) => {

setTimeout(() => {

done();

}, 2000);

};

  

// 默认任务，yarn gulp就行

exports.default = (done) => {

//...

done();

};

  

// 标记失败

exports.failTask = (done) => {

done(new Error("task fail"));

};

```

  

#### 串行任务

  

按顺序执行，阻塞

  

```javascript

const { series } = require("gulp");

exports.s1 = series("t1", "t2");

```

  

#### 并行任务

  

同时执行

  

```javascript

const { parallel } = require("gulp");

exports.p1 = series("t1", "t2");

```

  

#### 异步操作

  

- promise

```javascript

// 支持promise

exports.p1 = () => {

return Promise.resolve();

};

exports.p1Fail = () => {

return Promise.reject(new Error("task fail"));

};

```

- async/await

  

```javascript

// 支持async await

const timeout = (wait) => new Promise((resolve) => setTimeout(resolve, wait));

exports.a1 = async () => {

await timeout(3000);

};

```

  

- stream

  

```javascript

exports.s1 = () => {

const readStream = fs.createReadStream("src/source.txt");

const writeStream = fs.createWriteStream("dist/dist.txt");

readStream.pipe(writeStream);

return readStream;

};

```

  

#### 构建过程

  

```mermaid

graph LR

A[读取流] --> B[转换流] --> c[写入流]

```

  

```javascript

const { src, dest } = require("gulp");

const cleanCss = require("gulp-clean-css");

const rename = require("gulp-rename");

  

exports.sample = () => {

src("src/style/*.css") // 读取

.pipe(cleanCss()) //转换

.pipe(rename({ extname: ".min.css" })) //转换

.pipe(dest("dist/css")); // 写入

};

```

  

#### 插件

  

- 插件命名规范

  

```javascript

gulp - [name];

```

  

- 自动加载插件

  

```javascript

const loadPlugins = require("gulp-load-plugins");

// 使用时使用驼峰名

const plugins = loadPlugins(); // 自动加载全部插件的插件

```

  

[zce-gulp-demo](https://github.com/Tatekii/zce-gulp-demo)

  
