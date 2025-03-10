# 生成器
生成器是一种更简便创建迭代器的方法。通过 `function*` 语法定义，生成器函数返回一个生成器对象，该对象同时实现了[[Iterator#迭代协议|迭代器协议和可迭代协议]]。它可以**暂停执行并恢复**，允许按需生成值，适用于异步编程、迭代器、流处理等场景。

`Generator` 是隐藏类 [[Iterator]]的子类。

## 特性：
- 灵活的控制函数的每一步执行和终止

## 实例方法
- Generator.prototype.next()
	- 返回`yeild`表达式return的值
- Generator.prototype.return()
- Generator.prototype.throw()

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
`AsyncGenerator`对象由异步生成器函数返回，符合异步[[Iterator#迭代协议|迭代器协议和可迭代协议]]。

异步生成器方法总是产生 [`Promise`](Promise.md) 对象。

AsyncGenerator是[[Iterator#异步迭代器对象]]的子类。

## 异步生成器函数
```javascript
async function* foo() {
  yield await Promise.resolve('a');
  yield await Promise.resolve('b');
  yield await Promise.resolve('c');
}
```


