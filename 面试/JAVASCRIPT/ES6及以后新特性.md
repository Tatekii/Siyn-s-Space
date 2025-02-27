# ECAMSCRIPT

ECMAScript 是由 ECMA International（一个国际标准化组织）发布的标准化脚本语言规范，编号为 ECMA-262。ECMAScript 定义了脚本语言的基础语法、类型、语句、关键字、保留字、操作符、对象等。

JavaScript 是一种基于 ECMAScript 标准实现的编程语言。

# ECAMSCRIPT重要版本
以下是自 ES6 (ECMAScript 2015) 开始的每个 ECMAScript 版本及其新增的主要特性和语法：

### ECMAScript 2015 (ES6)

1. **块级作用域**：`let` 和 `const` 声明。
    ```javascript
    let x = 10;
    const y = 20;
    ```

2. **箭头函数**：简洁的函数表达式语法。
    ```javascript
    const add = (a, b) => a + b;
    ```

3. **模板字符串**：使用反引号 (`) 及插值 (`${expression}`)。
    ```javascript
    const name = "John";
    console.log(`Hello, ${name}!`);
    ```

4. **解构赋值**：从数组和对象中提取数据。
    ```javascript
    const [a, b] = [1, 2];
    const {name, age} = {name: "John", age: 30};
    ```

5. **默认参数**：为函数参数设置默认值。
    ```javascript
    function greet(name = "Guest") {
        console.log(`Hello, ${name}`);
    }
    ```

6. **类**：基于原型的面向对象编程的语法糖。
    ```javascript
    class Person {
        constructor(name) {
            this.name = name;
        }
        greet() {
            console.log(`Hello, my name is ${this.name}`);
        }
    }
    ```

7. **展开操作符**：展开数组或对象。
    ```javascript
    const arr = [1, 2, ...[3, 4]];
    const obj = {...{name: "John"}, age: 30};
    ```




8. **模块**：导入和导出模块。
    ```javascript
    // 导出
    export const name = "John";
    export function greet() { console.log("Hello"); }

    // 导入
    import { name, greet } from './module.js';
    ```

9. **Promise**：处理异步操作的新方式。
    ```javascript
    const promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve("Success"), 1000);
    });
    ```

10. **迭代器和生成器**：
    ```javascript
    function* generator() {
        yield 1;
        yield 2;
        yield 3;
    }
    ```

### ECMAScript 2016 (ES7)

11. **指数操作符**：
    ```javascript
    const result = 2 ** 3; // 8
    ```

12. **Array.prototype.includes**：
    ```javascript
    const arr = [1, 2, 3];
    console.log(arr.includes(2)); // true
    ```

### ECMAScript 2017 (ES8)

13. **异步函数**：`async` 和 `await` 关键字。
    ```javascript
    async function fetchData() {
        const response = await fetch('https://api.example.com/data');
        const data = await response.json();
        console.log(data);
    }
    ```

14. **Object.entries 和 Object.values**：
    ```javascript
    const obj = { a: 1, b: 2 };
    console.log(Object.entries(obj)); // [['a', 1], ['b', 2]]
    console.log(Object.values(obj)); // [1, 2]
    ```

15. **字符串填充**：`padStart` 和 `padEnd`。
    ```javascript
    const str = '5';
    console.log(str.padStart(2, '0')); // '05'
    console.log(str.padEnd(2, '0')); // '50'
    ```

16. **对象字面量中的尾逗号**：
    ```javascript
    const obj = {
        foo: 'bar',
        baz: 'qux',
    };
    ```

### ECMAScript 2018 (ES9)

17. **异步迭代器**：`for await...of`。
    ```javascript
    async function process(array) {
        for await (let item of array) {
            console.log(item);
        }
    }
    ```

18. **Rest/Spread 属性**：
    ```javascript
    const { a, ...rest } = { a: 1, b: 2, c: 3 }; // rest: { b: 2, c: 3 }
    const newObj = { ...rest, d: 4 }; // { b: 2, c: 3, d: 4 }
    ```

19. **Promise.prototype.finally**：
    ```javascript
    promise
        .then(result => console.log(result))
        .catch(error => console.log(error))
        .finally(() => console.log("Completed"));
    ```

20. **正则表达式改进**：
    - Unicode 转义序列。
    - s (dotAll) 标志：匹配任意字符（包括换行符）。
    ```javascript
    const regex = /foo.bar/s;
    ```

### ECMAScript 2019 (ES10)

21. **Array.prototype.flat 和 Array.prototype.flatMap**：
    ```javascript
    const arr = [1, [2, 3], [4, [5, 6]]];
    console.log(arr.flat(2)); // [1, 2, 3, 4, 5, 6]

    const arr2 = [1, 2, 3];
    console.log(arr2.flatMap(x => [x, x * 2])); // [1, 2, 2, 4, 3, 6]
    ```

22. **Object.fromEntries**：
    ```javascript
    const entries = [['a', 1], ['b', 2]];
    const obj = Object.fromEntries(entries); // { a: 1, b: 2 }
    ```

23. **String.prototype.trimStart 和 String.prototype.trimEnd**：
    ```javascript
    const str = '  hello  ';
    console.log(str.trimStart()); // 'hello  '
    console.log(str.trimEnd()); // '  hello'
    ```

24. **Optional Catch Binding**：catch 语句中的绑定变量是可选的。
    ```javascript
    try {
        // code
    } catch {
        // handle error
    }
    ```

25. **Function.prototype.toString 的修正**：函数的 toString() 方法返回准确的字符串表示。

### ECMAScript 2020 (ES11)

26. **动态 import**：
    ```javascript
    import('./module.js').then(module => {
        module.doSomething();
    });
    ```

27. **BigInt**：处理大整数的新数据类型。
    ```javascript
    const bigInt = 1234567890123456789012345678901234567890n;
    ```

28. **Nullish Coalescing 操作符 (`??`)**：
    ```javascript
    const foo = null ?? 'default'; // 'default'
    ```

29. **可选链操作符 (`?.`)**：
    ```javascript
    const user = { name: 'Alice', address: { city: 'Wonderland' } };
    const city = user?.address?.city; // 'Wonderland'
    ```

30. **Promise.allSettled**：
    ```javascript
    const promises = [Promise.resolve(1), Promise.reject('error')];
    Promise.allSettled(promises).then(results => console.log(results));
    ```

31. **全局对象 `globalThis`**：
    ```javascript
    console.log(globalThis);
    ```

32. **import.meta**：
    ```javascript
    console.log(import.meta.url);
    ```

33. **for-in 机制的改变**：对非枚举属性的行为进行了调整。

### ECMAScript 2021 (ES12)

34. **逻辑赋值操作符**：`&&=`, `||=`, `??=`
    ```javascript
    let a = 1;
    let b = 0;

    a &&= 2; // a = 2
    b ||= 2; // b = 2
    b ??= 3; // b = 2
    ```

35. **字符串的 replaceAll 方法**：
    ```javascript
    const str = 'foo foo';
    console.log(str.replaceAll('foo', 'bar')); // 'bar bar'
    ```

36. **Promise.any**：
    ```javascript
    const promises = [Promise.reject('error'), Promise.resolve(1)];
    Promise.any(promises).then(value => console.log(value)); // 1
    ```

37. **WeakRefs 和 FinalizationRegistry**：
    ```javascript
    let obj = {};
    const weakRef = new WeakRef(obj);
    const registry = new FinalizationRegistry(heldValue => {
        console.log(heldValue);
    });
    registry.register(obj, 'some value');
    ```

38. **类的私有方法和私有属性**：
    ```javascript
    class MyClass {
        #privateField = 42;
        #privateMethod() {
            return this.#privateField;
        }
        getPrivateField() {
            return this.#privateMethod();
        }
    }
    ```

### ECMAScript 2022 (ES13)

39. **类的静态块**：
    ```

javascript
    class MyClass {
        static {
            this.myStaticProperty = 'value';
        }
    }
    ```

40. **`Object.hasOwn`** 方法：
    ```javascript
    const obj = { a: 1 };
    console.log(Object.hasOwn(obj, 'a')); // true
    ```

41. **Top-level await**：在模块顶层使用 `await`。
    ```javascript
    const data = await fetch('https://api.example.com/data').then(res => res.json());
    console.log(data);
    ```

### ECMAScript 2023 (ES14)

42. **数组 `findLast` 和 `findLastIndex`**：
    ```javascript
    const arr = [1, 2, 3, 4];
    console.log(arr.findLast(x => x % 2 === 1)); // 3
    console.log(arr.findLastIndex(x => x % 2 === 1)); // 2
    ```

43. **哈希集合和哈希映射的 `with` 方法**：
    ```javascript
    const map = new Map([['key1', 'value1'], ['key2', 'value2']]);
    const newMap = map.with('key3', 'value3'); // 新创建一个包含新键值对的Map
    ```

