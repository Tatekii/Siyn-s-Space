**Block Formatting Context (BFC)** 是 CSS 排版中的一个重要概念，用于控制块级元素的布局行为，决定了元素如何相互影响，尤其是在 **块级元素**（如 div、section、p 等）中的排列方式。

### BFC 的特性
1. 内部盒子在垂直方向上陆续放置
2. BFC内的相邻盒子之间会出现外边距折叠
3. 每个元素的左外边距与包含块的左边界接触
4. BFC内的浮动元素参与包含块高度计算
5. BFC内的元素 **独立于外部的布局影响**，即该区域内的所有元素不会受到外部布局的影响。

### 创建BFC：

- 根元素`HTML`
- 浮动元素：`float` 不为 `none`
- 绝对定位元素：`position` 为 `absolute` 或 `fixed`
- 行内块元素`display` 为 `inline-block`
- 表格单元格`table-cell`，表格标题`table-caption`
- 匿名表格单元格元素（元素的 display 为 table、table-row、 table-row-group、table-header-group、table-footer-group（分别是HTML table、row、tbody、thead、tfoot 的默认属性）或 inline-table）
- `overflow`（计算值） 不为 `visible`
- 弹性元素`display` 为 `flex` 或 `inline-flex` 元素的直接子元素
- 网格元素`display` 为 `grid` 或 `inline-grid` 元素的直接子元素
- `display` 为 `flow-root`

### 用途
6. 清除浮动（浮动高度塌陷）
	1. [10.6.7 'Auto' heights for block formatting context roots](https://www.w3.org/TR/CSS2/visudet.html#root-height)
	2. 在父容器上应用 overflow: hidden 或 display: flow-root 来启用 BFC，(⚠️同时需要父元素height为'auto'默认值)
7. 防止外边距折叠
	1. BFC 还可以避免外边距折叠的问题，尤其是在垂直方向上，非同一个BFC内部的元素之间的外边距会独立存在，不会因为折叠而导致布局不正确。