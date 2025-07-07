# ECAMSCRIPT

ECMAScript 是由 ECMA International（一个国际标准化组织）发布的标准化脚本语言规范，编号为 ECMA-262。ECMAScript 定义了脚本语言的基础语法、类型、语句、关键字、保留字、操作符、对象等。

JavaScript 是一种基于 ECMAScript 标准实现的编程语言。

# ECAMSCRIPT重要版本
以下是自 ES6 (ECMAScript 2015) 开始的每个 ECMAScript 版本及其新增的主要特性和语法：

### ECMAScript 2015 (ES6)

1.  **块级作用域**：`let` 和 `const` 声明。
    ```javascript
    let x = 10;
    const y = 20;
    ```

2.  **箭头函数**：简洁的函数表达式语法，并且绑定了词法 `this`。
    ```javascript
    const add = (a, b) => a + b;
    ```

3.  **模板字符串**：使用反引号 (`) 及插值 (`${expression}`)，支持多行字符串。
    ```javascript
    const name = "John";
    console.log(`Hello, ${name}!`);
    ```

4.  **解构赋值**：从数组和对象中提取数据到独立变量。
    ```javascript
    const [a, b] = [1, 2];
    const {name, age} = {name: "John", age: 30};
    ```

5.  **默认参数**：为函数参数设置默认值。
    ```javascript
    function greet(name = "Guest") {
        console.log(`Hello, ${name}`);
    }
    ```

6.  **展开操作符 (`...`)**：展开数组或对象字面量。
    ```javascript
    const arr = [1, 2, ...[3, 4]]; // 数组合并
    const obj = {...{name: "John"}, age: 30}; // 对象合并/复制
    ```

7.  **对象字面量增强**：
    * **属性初始化简写**：当属性名和变量名相同时，可以省略。
    * **方法简写**：函数定义可以省略 `function` 关键字。
    * **计算属性名**：使用 `[]` 动态设置属性名。
    ```javascript
    const name = "John";
    const age = 30;
    const person = {
        name, // 属性初始化简写
        age,
        greet() { // 方法简写
            console.log(`Hello, my name is ${this.name}`);
        },
        ['user' + name]: true // 计算属性名
    };
    ```

8.  **Symbol**：新的原始数据类型，用于创建唯一且不可变的值，常用于对象属性，避免命名冲突。
    ```javascript
    const id = Symbol('id');
    const user = {
        [id]: 123,
        name: 'Alice'
    };
    console.log(user[id]); // 123
    ```

9.  **Map, WeakMap, Set, WeakSet**：
    * **Map**：键值对的集合，键可以是任意类型，保持插入顺序。
    * **WeakMap**：键值对的集合，键只能是对象，对键是弱引用，有助于垃圾回收。
    * **Set**：唯一值的集合，不包含重复元素，保持插入顺序。
    * **WeakSet**：唯一对象的集合，对对象是弱引用，有助于垃圾回收。
    ```javascript
    const map = new Map();
    map.set('key', 'value');

    const set = new Set([1, 2, 2, 3]); // Set { 1, 2, 3 }
    ```

10. **class**：基于原型的面向对象编程的语法糖，提供了更清晰的类定义方式。
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

11. **Modules (ESM)**：内置的模块系统，支持 `import` 和 `export` 语句，实现了模块的封装和复用。
    ```javascript
    // 导出 (module.js)
    export const name = "John";
    export function greet() { console.log("Hello"); }

    // 导入 (app.js)
    import { name, greet } from './module.js';
    ```

12. **Promise**：处理异步操作的新方式，避免回调地狱，支持链式调用。
    ```javascript
    const promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve("Success"), 1000);
    });
    ```

13. **Proxy 和 Reflect**：
    * **Proxy**：用于创建一个对象的代理，可以拦截并自定义该对象的各种基本操作（如属性查找、赋值、枚举、函数调用等）。
    * **Reflect**：一个内置对象，提供拦截 JavaScript 操作的方法，这些方法与 Proxy 对象的拦截器方法相对应，常与 Proxy 配合使用。
    ```javascript
    const handler = {
        get: function(obj, prop) {
            return prop in obj ? obj[prop] : 35;
        }
    };
    const p = new Proxy({}, handler);
    p.a = 1;
    console.log(p.a, p.b); // 1, 35
    ```

14. **Generator (生成器函数)**：创建迭代器的一种特殊函数，使用 `function*` 定义，通过 `yield` 关键字暂停和恢复执行。
    ```javascript
    function* idMaker() {
        let index = 0;
        while(true)
            yield index++;
    }
    const gen = idMaker();
    console.log(gen.next().value); // 0
    console.log(gen.next().value); // 1
    ```

---

### ECMAScript 2016 (ES7)

1. **指数操作符 (`**`)**：用于幂运算。
    ```javascript
    const result = 2 ** 3; // 8
    ```

2. **Array.prototype.includes()**：检查数组是否包含某个元素，返回布尔值。
    ```javascript
    const arr = [1, 2, 3];
    console.log(arr.includes(2)); // true
    console.log(arr.includes(4)); // false
    ```

---

### ECMAScript 2017 (ES8)

1.  **Async/Await**：基于 Promise 的异步编程语法糖，使异步代码看起来像同步代码，更易读和维护。
    ```javascript
    async function fetchData() {
        try {
            const response = await fetch('[https://api.example.com/data](https://api.example.com/data)');
            const data = await response.json();
            console.log(data);
        } catch (error) {
            console.error('Error fetching data:', error);
        }
    }
    ```

2.  **Object.entries(), Object.values()**：
    * **`Object.entries()`**：返回一个给定对象自身可枚举字符串键属性的 `[key, value]` 对数组。
    * **`Object.values()`**：返回一个给定对象自身可枚举字符串键属性值的数组。
    ```javascript
    const obj = { a: 1, b: 2 };
    console.log(Object.entries(obj)); // [['a', 1], ['b', 2]]
    console.log(Object.values(obj)); // [1, 2]
    ```

3.  **字符串填充**：**`String.prototype.padStart()` 和 `String.prototype.padEnd()`**。用于在字符串的开头或结尾填充字符，使其达到指定长度。
    ```javascript
    const str = '5';
    console.log(str.padStart(2, '0')); // '05'
    console.log(str.padEnd(2, '0')); // '50'

    const time = '9';
    console.log(time.padStart(2, '0')); // '09'
    ```

4.  **`Object.getOwnPropertyDescriptors()`**：返回指定对象上所有自身属性（包括不可枚举属性）的属性描述符。
    ```javascript
    const objDescriptor = {
        name: 'Alice',
        get age() { return 30; }
    };
    const descriptors = Object.getOwnPropertyDescriptors(objDescriptor);
    console.log(descriptors.name.value); // 'Alice'
    console.log(descriptors.age.get); // [Function: age]
    ```

---

### ECMAScript 2018 (ES9)

1.  **异步迭代器和 `for await...of`**：允许对异步可迭代对象进行迭代，使处理异步数据流更加方便。
    ```javascript
    async function* asyncGenerator() {
        yield Promise.resolve(1);
        yield Promise.resolve(2);
        yield Promise.resolve(3);
    }

    async function process() {
        for await (let item of asyncGenerator()) {
            console.log(item); // 1, 2, 3 (依次输出)
        }
    }
    process();
    ```

2.  **Rest/Spread 属性 (Object Rest/Spread Properties)**：
    * **Rest 属性**：在对象解构中收集剩余的属性到一个新对象。
    * **Spread 属性**：在对象字面量中复制现有对象的属性。
    ```javascript
    const { a, ...rest } = { a: 1, b: 2, c: 3 }; // rest: { b: 2, c: 3 }
    console.log(rest);

    const newObj = { ...rest, d: 4 }; // { b: 2, c: 3, d: 4 }
    console.log(newObj);
    ```

3.  **Promise.prototype.finally()**：在 Promise 无论成功或失败后都会执行的回调函数，常用于清理资源。
    ```javascript
    promise
        .then(result => console.log(result))
        .catch(error => console.log(error))
        .finally(() => console.log("Promise Completed")); // 无论成功失败都会执行
    ```

4.  **正则表达式改进**：
    * **命名捕获组**：允许给正则表达式的捕获组命名，通过名字访问匹配结果，提高可读性。
        ```javascript
        const regex = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;
        const match = regex.exec('2023-10-27');
        console.log(match.groups.year); // '2023'
        ```
    * **`s` (dotAll) 标志**：使正则表达式中的 `.` (点) 字符匹配包括换行符在内的所有字符。
        ```javascript
        const text = `first line\nsecond line`;
        const regexDotAll = /first.line/s;
        console.log(regexDotAll.test(text)); // true
        ```
    * **Unicode 属性转义 (`\p{...}`, `\P{...}`)**：允许匹配具有特定 Unicode 属性的字符。
        ```javascript
        const unicodeRegex = /\p{Script=Greek}/u; // 匹配希腊字母
        console.log(unicodeRegex.test('α')); // true
        ```
    * **Lookbehind Assertions (后行断言)**：在正则表达式中匹配某个模式之前是否有特定的模式。
        ```javascript
        const regexLookbehind = /(?<=¥)\d+/; // 匹配¥符号后面的数字
        console.log('¥100'.match(regexLookbehind)); // ['100']
        ```

---

### ECMAScript 2019 (ES10)

1.  **Array.prototype.flat() 和 Array.prototype.flatMap()**：
    * **`Array.prototype.flat(depth)`**：创建一个新数组，其中所有子数组的元素递归地连接到该新数组中，`depth` 参数指定扁平化的层级。
        ```javascript
        const arr = [1, [2, 3], [4, [5, 6]]];
        console.log(arr.flat(2)); // [1, 2, 3, 4, 5, 6]
        console.log(arr.flat());  // [1, 2, 3, 4, [5, 6]] (默认 depth 为 1)
        ```
    * **`Array.prototype.flatMap(callback)`**：首先使用映射函数映射每个元素，然后将结果扁平化一层。
        ```javascript
        const arr2 = [1, 2, 3];
        console.log(arr2.flatMap(x => [x, x * 2])); // [1, 2, 2, 4, 3, 6]
        ```

2.  **Object.fromEntries()**：将一个 `[key, value]` 对的列表（如 `Map` 或 `Object.entries()` 的返回结果）转换为一个对象。
    ```javascript
    const entries = [['a', 1], ['b', 2]];
    const obj = Object.fromEntries(entries); // { a: 1, b: 2 }
    ```

3.  **String.prototype.trimStart() 和 String.prototype.trimEnd()**：从字符串的开头或结尾移除空白字符。
    * `trimStart()` 等同于 `trimLeft()`
    * `trimEnd()` 等同于 `trimRight()`
    ```javascript
    const str = '   hello   ';
    console.log(str.trimStart()); // 'hello   '
    console.log(str.trimEnd()); // '   hello'
    console.log(str.trim()); // 'hello' (ES5)
    ```

4.  **Optional Catch Binding**：`catch` 语句中的绑定变量是可选的，当不需要访问错误对象时可以省略。
    ```javascript
    try {
        // code that might throw an error
    } catch { // 不再需要 `(error)`
        console.log("An error occurred, but we don't need the error object.");
    }
    ```

5.  **Function.prototype.toString() 的修正**：现在 `Function.prototype.toString()` 返回的函数源代码表示更加准确，包括注释和空格。

---

### ECMAScript 2020 (ES11)

1. **动态 import (`import()`)**：允许在运行时按需加载模块，返回一个 Promise，可以用于条件加载或优化初始加载性能。
    ```javascript
    document.getElementById('myButton').addEventListener('click', async () => {
        const module = await import('./heavy-module.js');
        module.doSomething();
    });
    ```

2. **BigInt**：新的原始数据类型，用于表示和操作任意精度的大整数，解决了 JavaScript `Number` 类型精度限制（`Number.MAX_SAFE_INTEGER`）的问题。
    ```javascript
    const bigInt = 1234567890123456789012345678901234567890n;
    const result = bigInt + 1n;
    console.log(result);
    ```

3. **Nullish Coalescing 操作符 (`??`)**：一个逻辑操作符，当左侧操作数为 `null` 或 `undefined` 时，返回右侧操作数；否则返回左侧操作数。这与 `||` 不同，`||` 会在左侧为假值（如 `0`, `''`, `false`）时也返回右侧。
    ```javascript
    const foo = null ?? 'default string'; // 'default string'
    const bar = 0 ?? 'default number';   // 0
    const baz = '' ?? 'default empty';   // ''
    ```

4. **可选链操作符 (`?.`)**：允许访问嵌套对象属性而无需进行冗余的 `null` 或 `undefined` 检查，如果链中的某个引用为 `null` 或 `undefined`，表达式会短路并返回 `undefined`。
    ```javascript
    const user = { name: 'Alice', address: { city: 'Wonderland' } };
    const city = user?.address?.city; // 'Wonderland'
    const zipCode = user?.address?.zipCode; // undefined (安全访问不存在的属性)

    // 也可用于方法调用和数组访问
    const getName = user?.getName?.(); // 如果 user.getName 存在则调用
    const firstItem = user.items?.[0]; // 如果 user.items 存在且是数组则访问第一个元素
    ```

5. **Promise.allSettled()**：返回一个 Promise，该 Promise 在所有给定的 Promise 都已解决或拒绝后解析，并带有一个对象数组，每个对象表示对应 Promise 的结果（`status` 和 `value` 或 `reason`）。
    ```javascript
    const promises = [
        Promise.resolve(1),
        new Promise(resolve => setTimeout(() => resolve(2), 100)),
        Promise.reject('error')
    ];
    Promise.allSettled(promises).then(results => console.log(results));
    /* Output:
    [
      { status: 'fulfilled', value: 1 },
      { status: 'fulfilled', value: 2 },
      { status: 'rejected', reason: 'error' }
    ]
    */
    ```

---

### ECMAScript 2021 (ES12)

1. **逻辑赋值操作符**：结合逻辑操作符和赋值操作符，提供更简洁的语法。
    * **`&&=` (逻辑 AND 赋值)**：如果左侧为真，则将右侧赋值给左侧。
    * **`||=` (逻辑 OR 赋值)**：如果左侧为假，则将右侧赋值给左侧。
    * **`??=` (空值合并赋值)**：如果左侧为 `null` 或 `undefined`，则将右侧赋值给左侧。
    ```javascript
    let a = 1;
    let b = 0;
    let c = null;

    a &&= 2; // a = 2 (因为 a 是真值，所以赋值)
    b ||= 2; // b = 2 (因为 b 是假值 0，所以赋值)
    c ??= 3; // c = 3 (因为 c 是 null，所以赋值)
    console.log(a, b, c); // 2, 2, 3
    ```

2. **String.prototype.replaceAll()**：返回一个新字符串，其中所有匹配给定模式的子字符串都被替换为指定替换字符串。
    ```javascript
    const str = 'foo foo bar foo';
    console.log(str.replaceAll('foo', 'baz')); // 'baz baz bar baz'

    // 对于正则表达式，需要使用 /g 标志
    console.log(str.replaceAll(/foo/g, 'baz')); // 'baz baz bar baz'
    ```

3. **Promise.any()**：接收一个 Promise 可迭代对象，只要其中一个 Promise 成功解决，就返回该 Promise 的值。如果所有 Promise 都拒绝，则抛出 `AggregateError`。
    ```javascript
    const promises = [
        Promise.reject('Error 1'),
        new Promise(resolve => setTimeout(() => resolve('Success!'), 50)),
        Promise.resolve('Success 2')
    ];
    Promise.any(promises)
        .then(value => console.log(value)) // 'Success!' (最快解决的那个)
        .catch(error => console.error(error));

    // 所有都失败的情况
    const allFail = [
        Promise.reject('A'),
        Promise.reject('B')
    ];
    Promise.any(allFail)
        .catch(error => console.log(error)); // AggregateError: All promises were rejected
    ```

4. **WeakRefs 和 FinalizationRegistry**：
    * **`WeakRef` (弱引用)**：允许你创建一个对象的弱引用。如果该对象没有其他强引用，垃圾回收器可以回收它，即使弱引用仍然存在。
    * **`FinalizationRegistry`**：允许你在一个对象被垃圾回收时请求一个回调函数。
    ```javascript
    let obj = {};
    const weakRef = new WeakRef(obj); // 弱引用 obj

    const registry = new FinalizationRegistry(heldValue => {
        console.log(`Object with value '${heldValue}' was garbage collected.`);
    });
    registry.register(obj, 'some value'); // 当 obj 被回收时，回调会被调用

    // 此时 obj 可能被回收，但不能保证何时发生
    obj = null; // 移除强引用，允许垃圾回收器回收 obj
    // 之后某个时刻，FinalizationRegistry 的回调可能被触发
    ```

5. **类的私有方法和私有属性**：使用 `#` 前缀来定义私有成员，这些成员只能在类的内部访问，提供了更好的封装性。
    ```javascript
    class MyClass {
        #privateField = 42; // 私有属性

        #privateMethod() { // 私有方法
            return `Private method called, field value: ${this.#privateField}`;
        }

        getPrivateData() {
            return this.#privateMethod();
        }
    }
    const instance = new MyClass();
    console.log(instance.getPrivateData()); // "Private method called, field value: 42"
    // console.log(instance.#privateField); // SyntaxError
    ```

---

### ECMAScript 2022 (ES13)

1. **类的静态块 (Class Static Block)**：允许在类定义中定义一个静态初始化块，用于执行一次性的静态初始化逻辑，例如设置静态属性或执行复杂的静态计算。
    ```javascript
    class MyClass {
        static myStaticProperty;
        static anotherStaticProperty = 'initial value';

        static { // 静态块
            this.myStaticProperty = 'initialized in static block';
            // 可以在这里执行复杂的逻辑来初始化静态成员
            console.log('Static block executed');
        }
    }
    console.log(MyClass.myStaticProperty); // 'initialized in static block'
    ```

2. **`Object.hasOwn()` 方法**：一个更安全、更推荐的替代 `Object.prototype.hasOwnProperty.call(obj, prop)` 的方法，用于检查对象自身是否拥有某个属性（不包括原型链上的属性）。
    ```javascript
    const obj = { a: 1, b: 2 };
    const inheritedObj = Object.create({ c: 3 });
    inheritedObj.d = 4;

    console.log(Object.hasOwn(obj, 'a')); // true
    console.log(Object.hasOwn(obj, 'toString')); // false (来自原型链)

    console.log(Object.hasOwn(inheritedObj, 'd')); // true
    console.log(Object.hasOwn(inheritedObj, 'c')); // false (来自原型链)
    ```

3. **数组和字符串的 `at()` 方法**：允许使用负数索引从数组或字符串的末尾开始访问元素。
    ```javascript
    const arr = [1, 2, 3, 4, 5];
    console.log(arr.at(0));  // 1 (第一个元素)
    console.log(arr.at(-1)); // 5 (最后一个元素)
    console.log(arr.at(-2)); // 4 (倒数第二个元素)

    const str = 'Hello';
    console.log(str.at(0));  // 'H'
    console.log(str.at(-1)); // 'o'
    ```

4. **Top-level `await`**：允许在 ES Modules 的顶层直接使用 `await` 关键字，而无需将其包裹在 `async` 函数中。这使得模块可以异步地初始化，并且在所有 `await` 操作完成后才能被其他模块导入。
    ```javascript
    // module.mjs
    const response = await fetch('[https://api.example.com/data](https://api.example.com/data)');
    const data = await response.json();
    export const myData = data;

    // app.mjs
    import { myData } from './module.mjs'; // 只有当 module.mjs 中的 await 完成后才会执行
    console.log(myData);
    ```

5. **错误原因 (`Error.prototype.cause`)**：允许在创建 `Error` 对象时指定一个 `cause` 属性，用于表示当前错误的原因，有助于构建更清晰的错误链。
    ```javascript
    function fetchData() {
        try {
            // 模拟一个错误
            throw new Error('Network error', { cause: new TypeError('Failed to fetch') });
        } catch (error) {
            console.error('Outer error:', error.message); // Outer error: Network error
            console.error('Cause:', error.cause); // Cause: TypeError: Failed to fetch
        }
    }
    fetchData();
    ```

---

### ECMAScript 2023 (ES14)

1.  **数组新方法 (Non-Mutating Array Methods)**：为了避免修改原数组，引入了一组新的非破坏性数组方法，它们返回新数组而不是修改原数组。
    1.  **`Array.prototype.toSorted(compareFn)`**: 返回一个元素按顺序排序的新数组，不改变原数组。
        ```javascript
        const arr = [1, 3, 2];
        console.log(arr.toSorted()); // [1, 2, 3]
        console.log(arr); // [1, 3, 2] (原数组未改变)
        ```
    2.  **`Array.prototype.toSpliced(start, deleteCount, ...items)`**: 返回一个通过添加或删除指定元素生成的新数组，不改变原数组。
        ```javascript
        const arr2 = [1, 2, 3, 4];
        console.log(arr2.toSpliced(1, 1, 5)); // [1, 5, 3, 4]
        console.log(arr2); // [1, 2, 3, 4]
        ```
    3.  **`Array.prototype.toReversed()`**: 返回一个元素顺序颠倒的新数组，不改变原数组。
        ```javascript
        const arr3 = [1, 2, 3];
        console.log(arr3.toReversed()); // [3, 2, 1]
        ```
    4.  **`Array.prototype.with(index, value)`**: 返回一个新数组，其中指定索引的元素被替换为新值，不改变原数组。
        ```javascript
        const arr4 = [1, 2, 3];
        console.log(arr4.with(1, 10)); // [1, 10, 3]
        ```
    5.  **`Array.prototype.findLast(predicate)`**: 类似于 `find()`，但从数组末尾开始查找，返回最后一个满足条件的元素。
        ```javascript
        const arr5 = [{id: 1}, {id: 2}, {id: 1}];
        console.log(arr5.findLast(item => item.id === 1)); // {id: 1} (第二个对象)
        ```
    6.  **`Array.prototype.findLastIndex(predicate)`**: 类似于 `findIndex()`，但从数组末尾开始查找，返回最后一个满足条件的元素的索引。
        ```javascript
        const arr6 = [{id: 1}, {id: 2}, {id: 1}];
        console.log(arr6.findLastIndex(item => item.id === 1)); // 2
        ```

2.  **Symbols 作为 WeakMap Keys**：现在允许使用 **Symbols** 作为 `WeakMap` 的键。这扩展了 `WeakMap` 的用途，允许将额外的信息关联到值上，而不会影响垃圾回收。
    ```javascript
    const weakMap = new WeakMap();
    const mySymbol = Symbol('description'); // 必须是注册的 Symbol 或新创建的 Symbol
    weakMap.set(mySymbol, 'value');
    console.log(weakMap.get(mySymbol)); // 'value'
    ```

3.  **Hashbang Grammar (Shebang)**：ECMAScript 2023 正式支持 **Hashbang (#! ) 语法**。这允许 JavaScript 文件像其他脚本一样，直接在支持 `shebang` 的类 Unix 环境中执行，通过在文件顶部指定解释器路径。
    ```javascript
    #!/usr/bin/env node
    console.log("Hello from a shebang script!");
    ```

4.  **Well-Formed Unicode Strings**：改进了 JavaScript 处理 Unicode 字符串的方式，引入了两个新方法来确保文本的正确格式化：
    * **`String.prototype.isWellFormed()`**: 检查字符串是否根据 Unicode 标准正确编码。
    * **`String.prototype.toWellFormed()`**: 返回一个新字符串，其中任何格式错误的序列都被替换为 Unicode 替换字符 (U+FFFD)。
    ```javascript
    const str1 = 'Hello\uD800World'; // 格式错误的字符串 (孤立代理对)
    console.log(str1.isWellFormed()); // false
    console.log(str1.toWellFormed()); // 'HelloWorld' (U+FFFD 替换)

    const str2 = 'Hello World';
    console.log(str2.isWellFormed()); // true
    ```

---

### ECMAScript 2024 (ES15)

1.  **`Object.groupBy()` 和 `Map.groupBy()`**：这两个新方法允许开发者根据回调函数返回的值对对象或 Map 的元素进行分组。
    ```javascript
    const products = [
        { name: 'Apple', category: 'Fruit' },
        { name: 'Carrot', category: 'Vegetable' },
        { name: 'Banana', category: 'Fruit' }
    ];

    const groupedByCategory = Object.groupBy(products, product => product.category);
    console.log(groupedByCategory);
    // {
    //   Fruit: [ { name: 'Apple', category: 'Fruit' }, { name: 'Banana', category: 'Fruit' } ],
    //   Vegetable: [ { name: 'Carrot', category: 'Vegetable' } ]
    // }

    const numbers = [1, 2, 3, 4, 5];
    // Map.groupBy 返回的是一个 Map 实例
    const groupedByParity = Map.groupBy(numbers, num => (num % 2 === 0 ? 'even' : 'odd'));
    console.log(groupedByParity.get('even')); // [2, 4]
    console.log(groupedByParity.get('odd'));  // [1, 3, 5]
    ```

2.  **Temporal API (提案阶段)**：旨在取代现有 `Date` 对象的局限性，提供一种现代、精确且用户友好的方式来处理日期和时间。它提供了不可变的日期时间对象，解决时区、夏令时和复杂日期计算等问题。
    * **注意**：此 API 仍在提案阶段，实际使用可能需要 Polyfill。

3.  **正则 `v` 标志 (RegExp `v` Flag)**：引入了正则表达式的 `v` 标志（Set Notation and Properties of Strings），允许更复杂的模式匹配和字符串操作，尤其是在处理具有特定 Unicode 属性的字符组时，可以创建更精确和可读的正则表达式。它增强了对 Unicode 字符集和字符串集合的操作能力。
    ```javascript
    // 匹配包含 emoji 的字符串，使用 v 标志可以正确处理 Unicode 字符属性
    const regex = /\p{Emoji_Modifier_Base}\p{Emoji_Modifier}?/v;
    console.log(regex.test('👍')); // true
    ```

4.  **Promise.withResolvers()**：简化了 Promise 的创建，通过提供 Promise 对象以及其独立的 `resolve` 和 `reject` 方法，使得手动创建 Promise 更方便，避免了传统构造函数中 `resolve` 和 `reject` 闭包的复杂性。
    ```javascript
    const { promise, resolve, reject } = Promise.withResolvers();

    promise.then(value => console.log(value))
           .catch(error => console.error(error));

    setTimeout(() => {
        resolve('Hello from withResolvers!');
    }, 1000);
    ```

5.  **ArrayBuffer Transfer**：对 `ArrayBuffer` 和 `SharedArrayBuffer` 进行了改进，支持更高效的内存管理和线程间通信。这主要涉及底层优化，例如 `structuredClone()` 现在支持转移 `ArrayBuffer`，而不是复制它们。

---

### ECMAScript 2025 (ES16)

1.  **迭代器辅助方法 (Iterator Helpers)**：引入了一系列新的迭代器方法，使得处理迭代器更高效，因为它们是惰性求值的（按需计算）。
    * **类似数组的方法**: 
	    * `iterator.filter()`
	    * `iterator.map()`
	    * `iterator.flatMap()`
	    * `iterator.some()`
	    * `iterator.every()`
	    * `iterator.find()`
	    * `iterator.reduce()`
	    * `iterator.forEach()`。
    * **迭代器独有的方法**:
        * **`iterator.drop(limit)`**: 返回一个不包含前 `limit` 个元素的迭代器。
        * **`iterator.take(limit)`**: 返回一个只包含前 `limit` 个元素的迭代器。
        * **`iterator.toArray()`**: 将迭代器的所有剩余元素收集到一个数组中并返回。
        * **`iterator.from(iterable)`**: 静态方法，从可迭代对象创建迭代器。
    ```javascript
    function* numbers() {
        yield 1;
        yield 2;
        yield 3;
        yield 4;
        yield 5;
    }

    const evenNumbers = numbers().filter(n => n % 2 === 0).toArray();
    console.log(evenNumbers); // [2, 4]

    const firstTwo = numbers().take(2).toArray();
    console.log(firstTwo); // [1, 2]

    const afterDrop = numbers().drop(2).toArray();
    console.log(afterDrop); // [3, 4, 5]
    ```

2.  **Set 方法增强 (Set Method Enhancements)**：为 `Set` 对象引入了新的方法，以简化常见的集合操作。
    * **集合组合**: 
	    * `Set.prototype.union(other)`并集A∪B
	    * `Set.prototype.intersection(other)`交集A∩B
	    * `Set.prototype.difference(other)`A∖B
	    * `Set.prototype.symmetricDifference(other)`AΔB=(A∪B)∖(A∩B)
    * **集合关系检查**: 
	    * `Set.prototype.isSubsetOf(other)` 子集
	    * `Set.prototype.isSupersetOf(other)`超集
	    * `Set.prototype.isDisjointFrom(other)`不相交
    ```javascript
    const setA = new Set([1, 2, 3]);
    const setB = new Set([3, 4, 5]);
    const setC = new Set([6, 7]);

    console.log(setA.union(setB));        // Set { 1, 2, 3, 4, 5 }
    console.log(setA.intersection(setB)); // Set { 3 }
    console.log(setA.difference(setB));   // Set { 1, 2 }
    console.log(setA.symmetricDifference(setB)); // Set { 1, 2, 4, 5 }
    console.log(setA.isSubsetOf(new Set([1, 2, 3, 4]))); // true
    console.log(setA.isDisjointFrom(setC)); // true
    ```

3.  **Float16Array 和 Math.f16round()**：增加了对 16 位浮点数的支持，主要用于图形处理和机器学习等需要节省内存和处理大量数字的场景。
    * **`Float16Array`**: 新的 TypedArray 类型，用于存储 16 位半精度浮点数。
    * **`DataView.prototype.getFloat16()` / `setFloat16()`**: 用于从 `ArrayBuffer` 中读写 16 位浮点数。
    * **`Math.f16round()`**: 对数字进行舍入，使其成为最近的 16 位浮点数表示。
    ```javascript
    // 创建一个 Float16Array
    const float16Arr = new Float16Array(2);
    float16Arr[0] = 0.125; // 刚好可以精确表示
    float16Arr[1] = 0.123; // 需要舍入
    console.log(float16Arr); // 输出类似 [0.125, 0.123046875] (取决于环境对16位浮点的处理)

    // Math.f16round 模拟 16 位浮点数的舍入行为
    console.log(Math.f16round(0.123)); // 0.123046875 (近似的 16 位浮点数)
    ```

4.  **正则表达式模式修饰符 (Inline Flags)**：允许在正则表达式的内部使用 `(?<flags>...)` 语法来局部地开启或关闭标志，例如 `i` (不区分大小写), `s` (dotAll)。
    * **重复的命名捕获组**: 现在可以在不同替代方案 (`|`) 中使用相同的组名，这使得处理复杂正则表达式更加灵活。
    ```javascript
    const text = 'APPLE banana';
    // (?i:...) 局部开启不区分大小写
    const regex = /(?i:apple)|(?<fruit>banana)/;
    const match1 = regex.exec(text);
    console.log(match1[0]); // 'APPLE'

    const text2 = 'Hello\nworld';
    // (?s:...) 局部开启 dotAll 模式
    const regex2 = /Hello(?s:.)world/;
    console.log(regex2.test(text2)); // true
    ```

---

### 正在提案中或未来版本可能出现的功能

除了已发布版本的功能，TC39（ECMAScript 标准委员会）还在持续推进许多提案，这些提案可能在未来版本中成为标准：

* **Records and Tuples**: 引入深度不可变的数据结构，类似于对象和数组，但具有不可变性，可以安全地进行值比较。
* **Decorators**: 允许以声明式方式修改类及其成员的行为。
* **Pipeline Operator (`|>`)**: 允许将一个表达式的结果“管道”到下一个函数中，以更函数式的方式组织代码，提高可读性。
* **Pattern Matching**: 提供一种新的语法，用于对复杂数据结构进行更简洁和可读的条件检查。
* **Async Context Propagation**: 旨在解决异步操作中上下文丢失的问题，使得在不同异步任务之间传递上下文信息更加容易。
* **BigDecimal**: 一种新的原始类型，用于处理任意精度的十进制数字，避免浮点数计算中的精度问题。