基于 Git+Npm 的多 package 项目管理工具

1. 解决原生脚手架开发中的重复操作
	- 多 package 本地 link
	- 多 package 依赖安装
	- 多 package 单元测试
	- 多 package 代码提交
	- 多 package 代码发布

2. 解决版本一致性
	- package 版本一致
	- 相互依赖版本升级

## lerna 开发 module 流程
1. 项目创建
```javascript

npm init

npm i lerna

lerna init

```

1. 创建 package

```javascript

lerna create // 创建package

lerna add //安装依赖

lerna //链接依赖

```

1. 开发测试

```javascript

lerna exec // 执行shell脚本

lerna run

lerna clean // 清空依赖

lerna bootstrap // 重装依赖

```

1. 发布上线

```javascript

lerna version // 提升版本号

lerna change // 查看变更

lerna diff // 查看diff

lerna public // 发布

```

  
