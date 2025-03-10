
- 命名规范`generator-<name>`
- 目录规范

```javascript

app - // 默认生成器

index.js - // 默认实现

other - // 其他生成器

index.js - // 其他生成器实现

package.json; // 模块包配置文件

```

#### Yeoman使用

1. 安装依赖

```javascript

// ./generator-xxx

  

yarn init

yarn add yeoman-generator // 必要依赖

```

  

2. 自定义generator

  

```javascript

// ./app/index.js

const Generator = require("yeoman-generator");

  

// ./app/index.js

module.exports = class extends Generator {

writing() {

// 写入文件

this.fs.write(this.destinationPath("random.txt"), Math.random().toString());

}

};

```

  

3. 执行generator

  

```bash

yarn link

  

cd desktop

  

yo <你的generator名>

  

# desktop/random.txt

# 随机数

```

  

4. 接收用户输入

  

```javascript

prompting() {

return this.prompt([

{

type: "input",

name: "name",

message: "your project name",

default: this.appname,

},

]).then((a) => {

this.answers = a

// 挂在到全局对象answers

// { name: '123' }

});

}

```

  
