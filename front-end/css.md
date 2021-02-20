##### CSS语法

1. CSS由2个主要部分构成：
   1. 选择器：选择需要改变样式的HTML元素
   2. 一条或多条声明：每条声明由一个属性和一个值组成
      1. 属性和值之间用冒号隔开
      2. 声明之间用分号隔开
      3. 声明用大括号括起来
      4. 最后以分号结束
2. 颜色的写法：
   1. 英文单词：`p {color: "red";}`
   2. 十六进制颜色值：`p {color: #ff0000;}`
   3. 缩写形式：`p {color: #f00;}`
   4. 使用RGB值：
      1. `p {color: rgb{255, 0, 0};}`
      2. `p {color: rgb(100%, 0%, 0%);}`
3. 注释：用`/**/`包括

##### 选择器

1. id选择器：以`#`开头，根据id来寻找HTML元素
2. class选择器：以`.`开头，也可以与其他的选择器组合使用
3. 标签选择器
4. 内联选择器，也就是在标签中使用`style`属性

##### CSS创建

1. 有外部样式表，也就是存储在`.css`文件中的
2. 还有内部样式表，存储在`.html`文件中的`<head></head>`标签中
3. 当某些标签被多个选择器选中，并被赋予了不同的属性时，将会使用那个选择器更详细的声明中的属性
4. 外部样式表的优先级不如内部样式表，当它们冲突时，优先选择内部样式表中的属性
5. 优先级仅与选择器有关，下面是选择器的优先级顺序（由低到高）：
   1. 通用选择器（*）
   2. 元素选择器
   3. 类选择器
   4. 属性选择器
   5. 伪类
   6. ID选择器
   7. 内联样式
6. 5中有一个例外，那就是如果`!important`规则被应用到声明中时，该声明会覆盖CSS中任何其他声明，使用它不是一个好习惯，所以在使用时要注意：
   1. 
7. 如果是组合选择器，可以用下面的权重来算得最后的分数，然后使用分数最高的选择器所对应的声明：
   1. 内联样式：1000
   2. ID选择器：100
   3. 类选择器：10
   4. 元素选择器：1

##### CSS背景

1. 背景图片：`background-image:url('img_tree.png');`
2. 平铺方法：`background-repeat:no-repeat;`
3. 放置位置：`background-position:`
   1. 位置关键字，下面5中可以组合两个：
      1. center
      2. top
      3. bottom
      4. right
      5. left
   2. 百分数值：`50% 50%;`
   3. 长度值：`50px 100px;`
4. 还可以将这些属性合并到同一个属性中：`body {background:#ffffff url('img_tree.png') no-repeat right top;}`

##### CSS文本

1. `color`
2. `text-align`
3. `text-decoration`
4. `text-transform`
5. `text-indent`
6. `word-spacing`

##### CSS字体

1. `font-family`：用来设置字体。
   1. 可以设置多个字体作为后备字体，这样当浏览器不支持前面的字体时可以使用后面的
2. `font-style`：字体样式
   1. `normal`
   2. `italic`
   3. `oblique`
3. `font-size`：字体大小

##### CSS链接

1. 链接有不同的状态，可以使用选择器来选择：
   1. `a:link`：正常，未访问过的链接
   2. `a:visited`：已访问过的链接
   3. `a:hover`：当用户鼠标放在链接上时
   4. `a:active`：链接被点击的那一刻
2. 文本修饰：`text-decoration`，主要用于删除链接中的下划线
3. 背景颜色：`background-color`

##### 列表样式

1. html中只能指定是有序列表还是无序列表
2. `list-style-type`：列表项标记，有下面几种选项：
   1. `non`：不使用项目符号
   2. `disc`：实心圆
   3. `circle`
   4. `square`
   5. `decimal`：阿拉伯数字
   6. `lower-alpha`：小写英文字母
   7. `upper-alpha`
   8. `lower-roman`
   9. `upper-roman`
3. `list-style-image`：列表标记图像
4. 还可以简写上面的这些

##### CSS表格

1. 

##### CSS盒子模型

1. 所有的HTML元素都可以看做盒子
2. ![css0](/home/lzh/Pictures/css0.png)
3. 不同部分说明：
   1. Margin：清除边框区域，它没有背景颜色，是完全透明的
   2. Border：边框周围的填充和内容，会受到盒子的背景颜色的影响
   3. Padding：内边距，内容周围的区域，会受到框中填充的背景颜色影响
   4. Content：内容，显示文本和图像
4. 当指定CSS元素的高宽时，实际上是设置内容区域的高宽

##### 边框

1. 可以使用如下属性来设置边框的属性：
   1. `border-style`
   2. `border-width`
   3. `border-color`
2. 上面的是设置整体的，还可以只设置单个边框的：`border-left`

##### 轮廓属性

1. 