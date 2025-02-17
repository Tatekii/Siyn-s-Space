（Block Formatting Context） 块级格式化上下文,是一个对块级盒子的布局及浮动相互影响有特殊规则的区域。

## 特性

> 一句话就是BFC内外不会互相影响。。。

- BFC内盒子之间会发生外边距合并

- BFC内浮动元素参与高度计算（高度塌陷）

- BFC内盒子不会超出BFC边界（浮动覆盖）

## 创建BFC：

- 根元素`HTML`

- 浮动元素：`float` 不为 `none`

- 绝对定位元素：`position` 为 `absolute` 或 `fixed`

- `overflow` 不为 `visible`

- `display` 为 `flow-root`

- 行内块元素`display` 为 `inline-block`

- 弹性元素`display` 为 `flex` 或 `inline-flex` 元素的直接子元素

- 网格元素`display` 为 `grid` 或 `inline-grid` 元素的直接子元素

- 表格元素`display` 为 `table``table-cell`或`table-xxx`等匿名表格元素