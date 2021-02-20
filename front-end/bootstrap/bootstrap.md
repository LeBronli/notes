#### CSS

1. 为了让其正常工作，要使用HTML5文件类型，所以要在文件开头包含下面代码

   `<!DOCTYPE html>`

2. 虽然从Bootstrap3开始，原生支持移动设备，但是为了确保适当的绘制和触屏缩放，需要在网页的`head`中添加`viewport meta`标签

   `<meta name="viewport" content="width=device-width, initial-scale=1.0">`

3. 通过添加下面的`img-responsive class`可以让图像对于响应式布局的支持更加友好

   `<img src="..." class="img-responsive">`

##### 网格系统

1. 随着屏幕尺寸的增加，系统会自动分为最多12列![bootstrap0](/home/lzh/Pictures/bootstrap0.png)
2. 