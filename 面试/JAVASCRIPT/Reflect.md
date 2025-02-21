# `Reflect`
操作对象的API
早起ECMA规范中没有考虑对对象本身的操作的API设计，就放在了`Object`上，而`Object`作为构造函数并不合适。
- 对象操作能返回布尔值
- `Object`未来可期能变得整洁
- 将命令式对象操作函数化，能抛出错误并返回执行结果
>	某些Object操作是命令式，比如name in obj和delete obj[name]，而Reflect.has(obj, name)和Reflect.deleteProperty(obj, name)让它们变成了函数行为。
- api与[[Proxy]]中方法均对应，能做到不直接操作原对象
```javascript
const handlerWithReflect = {
  get: function (target, prop, receiver) {
    console.log("get handler with Reflect called");
    return Reflect.get(...arguments);
  },

  set: function (target, prop, value, receiver) {
    console.log("set handler with Reflect called");
    return Reflect.set(...arguments);
  },
};

const proxyWithReflect = new Proxy(target, handlerWithReflect);

```