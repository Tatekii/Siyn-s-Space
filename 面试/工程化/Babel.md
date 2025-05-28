## polyfill

### 两种策略
#### **`core-js` (全局 Polyfill)**:
    
- **作用**：提供 ES6+ 新特性（如 `Promise`, `Map`, `Set`, `Array.prototype.includes` 等）的实现，以在旧环境中运行。
- **污染性**：默认情况下会污染全局作用域。
- **使用场景**：主要用于**应用程序 (Applications)**。在应用程序中，通常只需要确保所有代码都能在目标浏览器中运行，全局污染通常不是一个大问题，因为你的应用程序是最终的消费者。
- **配合 Babel**：
	- `@babel/preset-env` 的 `useBuiltIns: 'entry'` 选项会在入口文件处插入所有必要的 `core-js` 导入，根据你的目标环境进行筛选。
	- `@babel/preset-env` 的 `useBuiltIns: 'usage'` 选项更智能，它会根据你的代码中实际使用的 ES6+ 特性，自动按需导入所需的 `core-js` 模块，这样可以减少不必要的 Polyfill。
#### **`@babel/runtime` (无全局污染的 Helper 和 部分 Polyfill)**:
    
- **作用**：
	- **提供 Babel 生成的 Helper 函数**：这是其主要目的。例如，`class` 语法转换后需要 `_classCallCheck` 或 `_defineProperties` 等辅助函数。`@babel/runtime` 将这些 helper 函数作为模块提供，避免在每个文件重复生成，从而减少代码体积。
    - **提供模块化的 Polyfill (通过 `core-js` 的模块化版本)**：`@babel/runtime` 也包含了一小部分通过 `core-js` 模块化方式提供的 Polyfill，例如 `Symbol` 和 `Promise` 等。但请注意，它的 Polyfill 覆盖面不如直接导入 `core-js` 广。
- **污染性**：**不会污染全局作用域**。它将所有需要的 helper 和 Polyfill 作为模块导入，你的代码会引用这些模块，而不是直接修改全局对象。
- **使用场景**：主要用于**库 (Libraries) 或组件 (Components)**。当你开发一个库时，你不能假设你的用户会提供哪些全局 Polyfill，或者你不能去污染他们的全局环境。`@babel/runtime` 确保你的库是自包含的，并且不会与用户环境冲突。

### 组合使用
- **对于应用程序 (Applications)**：

    - 使用 `@babel/preset-env` 结合 `useBuiltIns: 'usage'` 或 `'entry'` 来处理 Polyfill（依赖 `core-js`），以确保你的应用程序能够运行在目标浏览器中。**在这种情况下，你接受 `core-js` 对全局变量的污染，因为它在应用程序层面是可接受的。**
    - 同时，也使用 `@babel/plugin-transform-runtime` 来处理 Babel 生成的 helper 函数，以避免这些 helper 函数的重复。

- **对于库或组件 (Libraries/Components)**：
    
    - **不直接导入 `core-js` 来污染全局**。
    - **完全依赖 `@babel/plugin-transform-runtime`**。这确保了你的库不会修改全局环境，并且所有需要的 helper 函数和极少数的模块化 Polyfill（例如 `Symbol`、`Promise` 等，通过 `core-js` 的模块化方式提供）都是通过导入模块的方式实现的。如果你的库需要更广泛的 Polyfill，你通常会要求用户在使用你的库之前自己提供这些 Polyfill。