# 生成器
生成器是一种更简便创建迭代器的方法。通过 `function*` 语法定义，生成器函数返回一个生成器对象，该对象同时实现了迭代器协议和可迭代协议[⏭️Iterator](./Iterator)。

### 面试题
- 实现自执行generator函数
```javascript
function co(generator) {

	const gen = generator()
	
	let ret
	
	try {
	
		ret = gen.next()
	
	} catch (e) {
	
		ret = gen.throw(e)
	
	}

	return step(ret)

  

	function step(res) {
	
		if (res.done) {
		
			return Promise.resolve(res.value)
		
		}

		// promise化
		
		if (!(res.value instanceof Promise)) {
		
			res.value = Promise.resolve(res.value)
		
		}

  

		return res.value.then(
			(v) => step(gen.next(v)),
			(e) => step(gen.throw(e))
		)

	}

}

  

// test

const gen1 = function* () {

	const res = yield Promise.resolve(1)
	
	const ret = yield Promise.resolve(res)

	return ret

}

  

co(gen1).then((v) => console.log(v)) // 1

```
```