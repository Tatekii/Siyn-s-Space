Grunt
```javascript

// __dir/gruntfile.js

modules.exports = (grunt) => {

grunt.registerTask("default", () => {

// 使用yarn grunt就能执行

});

grunt.registerTask("foo", "foo 的描述", () => {

// 使用yarn grunt foo执行

});

grunt.registerTask("default", ["foo", "bar"]);

};

```

  

#### 异步 task

  

grunt task 默认是同步代码

```javascript

module.exports = (grunt) => {

// 异步任务要获取this,不能用=>函数

grunt.registerTask("async-task", function () {

const done = this.async();

setTimeout(() => {

// ...

done(); // 调用这个done才结束task

}, 3000);

});

};

```

  

#### task 失败

  

- 在 task 中`return false`标记 task 失败

```javascript

grup.registerTask("fail-task", () => {

//...

return false;

});

```

- 在异步 task 中给 done 传 false 参数

```javascript

grup.registerTask("fail-async-task", function () {

const done = this.async();

//...

setTimeout(() => {

//...

done(false);

}, 3000);

});

```

- 如果是一个 task 数组，会在失败处中断

- 可以加上`--force`强行执行所有 task

  

#### task 配置

  

```javascript

module.exports = (grunt) => {

grunt.initConfig({

foo: {

bar: "666",

},

});

  

grunt.registerTask("foo", () => {

// 获取配置

console.log(grunt.config()); // { foo: { bar: '666' } }

console.log(grunt.config("foo")); // { bar: '666' }

console.log(grunt.config("foo.bar")); // 666

});

};

```

#### 多目标task

```javascript

grunt.initConfig({

"multi-task1": {

options: {

// 任务配置选项

foo: "bar",

},

// 除了options外都会注册为目标

t1: "1",

t2: {

options: {

foo: "baz",

},

name: "target222",

},

},

});

grunt.registerMultiTask("multi-task1", function () {

console.log(this.options(), this.data.options ? "options被覆盖" : "无覆盖的options");

console.log(`target=>${this.target};data=>${this.data.name}`);

// Running "multi:t1" (multi) task

// { foo: 'bar' } 无覆盖的options

// target=>t1;data=>undefined

  

// Running "multi:t2" (multi) task

// { foo: 'baz' } options被覆盖

// target=>t2;data=>target222

});

```

  

- 只执行单个目标

  

```javascript

yarn gulp multi-task1:[目标名]

```

  

#### grunt插件

  

[https://www.gruntjs.net/plugins](https://www.gruntjs.net/plugins)

命名规范:`grunt-contrib-[pluginName]`

  

- grunt-contrib-clean

```javascript

// 是一个多目标任务

grunt.initConfig({

// 配置插件

clean: {

cleanTestDirText: "test/*.txt", // 删除./test/*.txt

cleanTestAllDir: "testAll/*", // 删除./testAll/*

},

});

// 加载插件

grunt.loadNpmTasks("grunt-contrib-clean");

```

- grunt-sass

  

```javascript

grunt.initConfig({

// 配置插件

sass: {

options: {

sourceMap: true,

implementation: require("sass"),

},

main: {

// <!-- output input -->

"dist/style/main.css": "src/style/main.scss",

},

},

});

// 加载插件

grunt.loadNpmTasks("grunt-contrib-clean");

```

  

- grunt-babel

```javascript

// 使用merge方法添加配置，直接重写会覆盖

grunt.config.merge({

// 配置插件

babel: {

options: {

presets: ["@babel/preset-env"],

sourceMap: true,

},

main: {

files: {

"dist/js/app.js": "src/js/app.js",

},

// <!-- output input -->

},

},

});

// grunt.loadNpmTasks('grunt-contrib-clean')

// grunt.loadNpmTasks('grunt-sass')

require("load-grunt-tasks")(grunt, {

pattern: "grunt-babel", // 只匹配了babel

});

```

  

#### load-grunt-tasks

  

自动加载dependencies/devDependencies/peerDependencies/optionalDependencies中所有tasks

- 使用

  

```javascript

// 上面的grunt-sass可以改为

const load = require("load-grunt-tasks");

load(grunt);

load();

```

  

- 选项

  

```javascript

require("load-grunt-tasks")(grunt, {

pattern: "grunt-contrib-*", // 正则

config: "../package.json",

scope: "devDependencies", // load范围

requireResolution: true, // 在全部文件中查找而不仅是 node_modules

});

```

  