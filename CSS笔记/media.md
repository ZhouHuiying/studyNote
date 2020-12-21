### CSS3 @media 查询

使用 @media 查询，你可以针对不同的媒体类型定义不同的样式。
@media 可以针对不同的屏幕尺寸设置不同的样式，特别是如果你需要设置设计响应式的页面，@media 是非常有用的。

当你重置浏览器大小的过程中，页面也会根据浏览器的宽度和高度重新渲染页面。

如果文档宽度小于 300 像素则修改背景颜色(background-color):
  @media screen and (max-width: 300px) {
    body {
        background-color:lightblue;
    }
  }