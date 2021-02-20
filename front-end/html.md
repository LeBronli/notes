#### 简介

1. `<!DOCTYPE>`是用来识别网页内容的，使用HTML要用

   `<!DOCTYPE html>`

2. 定义元素：

   1. 标题是通过`<h1>-<h6>`标签来定义的
   2. 段落是通过标签`<p>`来定义
   3. 链接通过`<a>`定义，既可以用来链接到外部地址实现页面跳转，也能链接到本页面的某部分，从而实现内部导航
   4. 图像通过`<img>`来定义
   5. 使用`<em>`来强调元素，浏览器默认为斜体
   6. `<body>`元素定义文档主体
   7. `<html>`元素定义整个html文档

3. HTML中无论多少个连续的空格，在渲染的时候都会变成单独的空格符

##### 属性

1. `class`：类名
2. `id`：唯一id
3. `style`：规定行内样式
4. `title`：元素的额外信息，作为工具条使用

##### 标题

1. `<hr>`：可以用来创建水平线
2. `<! -- 注释 -->`：注释，渲染时会忽略

##### 段落

1. `<br>`：换行

##### 格式化

1. 文本格式化标签
2. 计算机输出标签
3. 引文，引用和标签定义

##### 链接

1. 使用#来表示空连接

   `<a href="#">空链接</a>`

2. Target属性可以定义被链接的文档在何处显示：

   target设为`_blank`，链接就可以在新窗口打开

3. 还可以在`href`中定义id，这样就可以跳转到相应的地方

   `a href="//www.w3cschool.cn/html_link.html#tips">链接</a>`

##### 头部

1. 头部是`<head></head>`元素包围的部分，描述了文档的各种属性和信息
2. 可以在头部添加的元素：
   1. `<title>`：浏览器工具栏的，收藏夹中的，以及搜索引擎结果标题
   2. `<base>`：描述基本的链接地址或者链接目标，所谓文档中所有链接的默认地址或默认目标
   3. `<link>`：定义了文档和外部资源之间的关系，通常用于链接到样式表
   4. `<style>`：定义文档的样式文件引用地址，指定样式文件来渲染文档
   5. `<meta>`：元数据，不会显示在页面上，但是对于机器是可读的，用于存储：
      1. 文档的描述
      2. 关键词
      3. 作者
      4. 最后修改时间
      5. 其他元数据
   6. `<script>`：加载脚本文件

##### CSS

1. 全名为`Cascading Style Sheet`，可以被译为层叠样式表
2. 可以使用几种方式添加：
   1. 内联样式：在元素中使用`style`属性
   2. 内部样式表：在`<head>`区域中使用`<style>`来包含CSS
   3. 外部引用：使用外部CSS文件

##### 图像

1. `alt`属性用来为图像定义一串预备的可替换的文本，即当图像加载不出来时，会使用替换文本替换
2. 属性：
   1. `width`：宽
   2. `height`：高
   3. `border`：以像素为单位设定边框的粗细
   4. `align`：对齐方式
3. 其他图像标签：
   1. `<map>`：定义图像地图
   2. `<area>`：定义图像地图中的可点击区域

##### 表格

1. 定义表格元素：

   1. 使用`<table>...</table>`定义表格
   2. `<th></th>`定义标题栏
   3. `<tr></tr>`定义行
   4. `<td></td>`定义列
   5. `<caption></caption>`定义标题

2. 表格属性

   1. `border`：定义边框
   2. `cellspacing`：单元格之间的空间
   3. `cellpadding`：边框与单元格之间的空间
   4. `bgcolor`：可以为整个或者单元格设置背景颜色
   5. `background`：设置背景图像
   6. `bordercolor`：设置边框颜色
   7. 还有前面定义的公共属性

3. 合并单元格

   1. 代码示例：

      ```html
      <table border = "1">
      
      <tr>
      
      <th>Column 1</th>
      
      <th>Column 2</th>
      
      <th>Column 3</th>
      
      </tr>
      
      <tr>
      
      <td rowspan = "2">Row 1 Cell 1</td>
      
      <td>Row 1 Cell 2</td>
      
      <td>Row 1 Cell 3</td>
      
      </tr>
      
      <tr>
      
      <td>Row 2 Cell 2</td>
      
      <td>Row 2 Cell 3</td>
      
      </tr>
      
      <tr>
      
      <td colspan = "3">Row 3 Cell 1</td>
      
      </tr>
      
      </table>
      ```

4. 表格分为3部分：

   1. 表头：`<thead>`
   2. 表格主体：`<tbody>`
   3. 表格尾：`<tfoot>`

5. 表格允许嵌套

##### 列表

1. 

##### 区块

1. 大多数HTML元素都被定义为块级元素或内联元素
   1. 块级元素通常会以新行来开始：`<h1>, <p>, <ul>, <table>`
   2. 内联元素不会以新行开始：`<b>, <td>, <a>, <img>`
2. `<div>`是块级元素，它可以用于组合其他HTML元素的容器
   1. 如果与CSS一同 使用，`<div>`元素可以用于对大块的内容设置样式属性
   2. 另一个用途是文档布局
3. `<span>`元素是内联元素，可以作为文本的容器
   1. 当与CSS一起使用时，可用于为部分文本设置样式属性

##### 布局

1. 大多数网站会像报纸一样将内容安排到多个列中
2. 大多数网站可以使用div元素来创建多列，用CSS对元素进行定位
3. 不建议使用table作为布局工具

##### 表单

1. 文本输入代码示例：

   ```html
   <form>
       姓名：<input type="text" name="firstname"><br>
       密码：<input type="password" name="pwd"><br>
   </form>
   ```

2. 单选示例

   ```html
   <form>        
   
   <input type="radio" name="sex" value="male">男<br>      
   
   <input type="radio" name="sex" value="female">女       
   
   </form>
   ```

3. 多选示例

   ```html
   <form>      
   
   <input type="checkbox" name="vehicle" value="Bike">我有自行车<br>      
   
   <input type="checkbox" name="vehicle" value="Car">我有小车
   
   </form>
   ```

4. 提交按钮示例

   ```html
   <form name="input" action="html_form_action.php" method="get">
   
   Username: <input type="text" name="user">
   
   <input type="submit" value="Submit">
   
   </form>
   ```

##### 框架

1. `<iframe>`标签灰顶一个内联框架，用来在当前HTML文档中嵌入另一个文档
2. 



