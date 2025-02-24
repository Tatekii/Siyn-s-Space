# 生成器
生成器是一种更简便创建迭代器的方法。通过 `function*` 语法定义，生成器函数返回一个生成器对象，该对象同时实现了迭代器协议和可迭代协议[⏭️Iterator](迭代器.md)。

特性：
- 灵活的控制函数的每一步执行和终止

### 面试题
- 实现自执行generator函数
```javascript

function co<T>(genFunc: () => Generator<Promise<T>, T, T>): Promise<T> {
  return new Promise((resolve, reject) => {
	// generator
    const generator = genFunc();
    
    function step(nextFn: (value?: any) => IteratorResult<Promise<T>, T>, arg?: any) {
      let result;
      
      try {
        result = nextFn(arg);
      } catch (error) {
        return reject(error);
      }

      if (result.done) {
        return resolve(result.value);
      }

      Promise.resolve(result.value)
        .then(val => step(generator.next.bind(generator), val))
        .catch(err => step(generator.throw.bind(generator), err));
    }

    step(generator.next.bind(generator));
  });

}
  

// test
const gen1 = function* () {
	const res = yield Promise.resolve(1)
	const res = yield new Promise(resolve=>{
		setTimeout(resolve,20000)
	})
	const ret = yield Promise.resolve(res)
	return ret
}

co(gen1).then((v) => console.log(v)) // 1

```

# 异步生成器
`AsyncGenerator`对象由异步生成器函数返回，符合[异步可迭代协议和异步迭代器协议](异步迭代器AsyncIterator.md#异步可迭代协议)。

异步生成器方法总是产生 [`Promise`](Promise.md) 对象。

AsyncGenerator是[异步迭代器AsyncIterator](异步迭代器AsyncIterator.md)的子类。

## 异步生成器函数
```javascript
async function* foo() {
  yield await Promise.resolve('a');
  yield await Promise.resolve('b');
  yield await Promise.resolve('c');
}
```


