# `Reflect`
操作对象的API
- 对象操作能返回布尔值
- 将对象操作函数化
>	某些Object操作是命令式，比如name in obj和delete obj[name]，而Reflect.has(obj, name)和Reflect.deleteProperty(obj, name)让它们变成了函数行为。
- 与[[Proxy]]中方法均对应
```javascript
const handlerWithReflect = {
  get: function (target, prop, receiver) {
    console.log("get handler with Reflect called");
    return Reflect.get(...arguments); // Uses `Reflect` to forward the operation
  },

  set: function (target, prop, value, receiver) {
    console.log("set handler with Reflect called");
    return Reflect.set(...arguments); // Uses `Reflect` to forward the operation
  },
};

const proxyWithReflect = new Proxy(target, handlerWithReflect);

```