## 触发事件
1. `onDrag`
- 被拖拽元素
1. `onDragStart`
2. `onDragOver`
3. `onDragEnd`
- 目标元素
5. `onDrop`
- 途中经过元素/目标元素
7. `onDragEnter`
8. `onDragLeave`
## DOMEvent
- `DragEvent`
	- 继承`MouseEvent`和`Event`
	- 指针设备触发
	- 通过[DragEvent.dataTransfer](https://developer.mozilla.org/zh-CN/docs/Web/API/DragEvent/dataTransfer)传输数据
## 数据传递
- [`DataTransfer.setData()`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/setData)
- [`DataTransfer.getData()`](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/getData)
## 树形控件上的执行流程
```mermaid
stateDiagram-v2

state if_leave <<choice>>
state if_drop <<choice>>

[*] --> 拖起节点
拖起节点 --> dataTransfer写入数据:onDragStart
dataTransfer写入数据 --> 被拖拽state为true
被拖拽state为true --> 关闭toolTip
关闭toolTip --> 拖动进入别的节点:onDrageEnter
拖动进入别的节点 --> 展示位置标识:计算`EVENT`和`EVENT.TARGET`相对位置
展示位置标识 --> 展开子节点
展开子节点 --> ifDrop:是否drop？
ifDrop --> drop元素:是
drop元素 --> 关闭位置标识:onDrop
关闭位置标识 --> [*]:dataTransfer读取数据重新渲染
ifDrop --> if_leave:离开节点？
if_leave --> 拖拽离开:是
if_leave --> [*]:否
拖拽离开 --> 关闭放置标识:onDragLeave
关闭放置标识 --> [*]


```