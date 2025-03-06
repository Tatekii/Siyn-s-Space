## 特性
### 异步更新
setState 不会立即更新状态，而是将状态更新操作加入到更新队列中，等待 React 完成组件的重新渲染。在调用 setState 后，立即读取 this.state 可能会得到旧的值。


如果你需要在状态更新后执行某个操作，应该利用 setState 的回调函数。setState 可以接受第二个参数，作为状态更新完成后的回调。
```javascript
this.setState({ count: this.state.count + 1 }, () => {
  console.log('State has been updated!');
});
```

### 批处理
React 会批量处理多个 setState 调用，尤其是在事件处理函数、生命周期方法、useEffect 钩子等环境中，多个 setState 调用会被合并成一个更新，以提高性能。

通过使用函数形式的 setState 来保证对前一个状态的正确引用。
```javascript
this.setState((prevState) => ({

  count: prevState.count + 1,

}));
```


一次性进行更新
```javascript
this.setState({ count: this.state.count + 1 });
this.setState({ count: this.state.count + 2 });
// 最终更新为 count: 3，但只触发一次渲染
```


### 浅层合并
React 的 setState 使用浅合并更新状态。这意味着如果你更新一个对象的属性，React 只会合并该对象的顶层属性，不会深度合并。对于嵌套对象或数组，React 只会替换掉它们，而不会进行深度更新。

如果你需要对嵌套的对象进行更新，应该手动合并数据，或者使用 spread 运算符。

```javascript
this.setState(prevState => ({
  user: {
    ...prevState.user,
    name: 'Bob',
  },
}));
```
