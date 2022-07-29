# css的width和height

## width:auto
1. 块级元素 会充分利用可用空间
2. 内联元素、table元素、绝对定位元素和浮动元素，由内容决定，将宽度收缩到一个合适的值。
3. 收缩到最小。如，table-layout为auto的表格中，由内容决定，将宽度收缩到最小值。
4. 溢出容器。如，内容很长的连续的英文和数字、内联元素被设置了white-space:nowrap。
## width: 100%  
width为其包含块的width
## 包含块
 一个元素的尺寸和位置经常受其包含块(containing block)的影响。大多数情况下，包含块就是这个元素最近的祖先块元素的内容区，但也不是总是这样。  
1. 如果 position 属性为 static 、 relative 或 sticky，包含块可能由它的最近的祖先块元素（比如说inline-block, block 或 list-item元素）的内容区的边缘组成，也可能会建立格式化上下文(比如说 a table container, flex container, grid container, 或者是 the block container 自身)。  
2. 如果 position 属性为 absolute ，包含块就是由它的最近的 position 的值不是 static （也就是值为fixed, absolute, relative 或 sticky）的祖先元素的内边距区的边缘组成。  
3. 如果 position 属性是 fixed，在连续媒体的情况下(continuous media)包含块是 viewport ,在分页媒体(paged media)下的情况下包含块是分页区域(page area)。  
4. 如果 position 属性是 absolute 或 fixed，包含块也可能是由满足以下条件的最近父级元素的内边距区的边缘组成的：  
    * transform 或 perspective 的值不是 none  
    * will-change 的值是 transform 或 perspective  
    * filter 的值不是 none 或 will-change 的值是 filter(只在 Firefox 下生效).  
    * contain 的值是 paint (例如: contain: paint;)