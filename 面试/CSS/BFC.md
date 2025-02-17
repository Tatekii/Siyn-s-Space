**Block Formatting Context (BFC)** 是 CSS 排版中的一个重要概念，用于控制块级元素的布局行为，决定了元素如何相互影响，尤其是在 **块级元素**（如 div、section、p 等）中的排列方式。

## 特性

> 一句话就是BFC内外不会互相影响。。。

- BFC内盒子之间会发生外边距合并
- 
- BFC中每个元素紧挨着包含块左边缘

- BFC内浮动元素参与高度计算（fix高度塌陷）

- BFC内盒子不会超出BFC边界（fix浮动覆盖）

### BFC 的行为与规则
BFC 的规则与行为主要包括以下几种：
#### **(1) 清除浮动元素**

BFC 最著名的特性之一就是它能够清除浮动（float）元素的影响。在 BFC 中，浮动元素的高度和宽度不会影响容器元素的高度。一个容器元素如果包含浮动元素，它的 **高度** 会自动 **扩展**，以包含所有的浮动元素。

• 例如，如果一个包含浮动元素的容器没有设置 height，它可能会因为浮动元素的存在而出现布局问题，BFC 会解决这个问题。
#### **(2) 防止外边距折叠**

在 BFC 中，**相邻的垂直外边距**（margin-top 和 margin-bottom）不会发生 **折叠**（collapsing）。即，BFC 会使得上下 **外边距** 保持独立，而不会互相压缩。

• 例如，两个相邻的块级元素如果没有 BFC，它们的外边距可能会发生折叠（即两个元素之间的空间可能小于它们各自的外边距），但在 BFC 中，它们的外边距保持各自的大小。

#### **(3) 限制容器的宽度**

BFC 使得元素的内容不会被溢出。具体来说，如果 BFC 中的元素有浮动或者内容溢出，BFC 会处理它们，确保内容不会溢出父容器。

  

**(4) 自适应宽度**

  

BFC 中的元素会尽可能 **扩展其宽度**，以填充父容器的可用空间。通常，块级元素会扩展到父容器的 **最大宽度**。

  

**(5) 阻止外部影响**

  

BFC 使得其中的元素 **独立于外部的布局影响**，即该区域内的所有元素不会受到外部布局的影响。

## 创建BFC：

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




