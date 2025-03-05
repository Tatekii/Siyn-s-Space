# ref
- 返回数据的ref对象，该对象的value属性进行了Proxy代理。

## shallowRef
- 不会被深层递归地转为响应式。

# reactive
- 只适用于对象数据Array,Object,Map,Set
- 返回一个对象的Proxy代理
- 解构会丢失响应式

## shallowReactive
- 不会被深层递归地转为响应式。