# ref
- 包装原始值或单一对象
- 返回数据的ref对象，该对象的value属性进行了Proxy代理。

## shallowRef
- 不会被深层递归地转为响应式。

# reactive
- 只适用于对象数据Array,Object,Map,Set
- 返回一个对象的Proxy代理

## shallowReactive
- 不会被深层递归地转为响应式。

| **Feature**         | ref                                                                                       | reactive                                         |
| ------------------- | ----------------------------------------------------------------------------------------- | ------------------------------------------------ |
| **Data Type**       | Primarily for **primitive values** (number, string, boolean) or a single reference object | **Objects and arrays**                           |
| **Accessing Value** | You must use .value to access or modify the data (except in templates)                    | Direct access (e.g., state.count)                |
| **Reactivity**      | Makes the wrapped primitive/reactive object reactive                                      | Makes all properties of an object/array reactive |
| **Use Case**        | Use when you need a **single reactive value** (primitives)                                | Use for **reactive objects/arrays**              |
>注意⚠️：
>结构赋值会丢失响应式绑定 