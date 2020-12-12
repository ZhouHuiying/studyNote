[官网](https://www.sass.hk/docs/)

### 1.sass中可以使用变量，通过 $ 来标识变量
  例如 $highlight-color: #F90;
  1-1变量声明 
    任何可以用作css属性值的赋值都 可以用作sass的变量值，甚至是以空格分割的多个属性值。
  1-2变量引用
    凡是css属性的标准值（比如说1px或者bold）可存在的地方，变量就可以使用。

### 2.嵌套CSS 规则
  2-1. 父选择器的标识符&
    article a {
      color: blue;
      &:hover { color: red }
    }
  2-2. 群组选择器的嵌套
    .button button会命中button元素和类名为.button的元素。这种选择器称为群组选择器。
      .button, button {
        margin: 0;
      }
  2-3. 子组合选择器和同层组合选择器：>、+和~
      article section { margin: 5px }：选择article下所有命中的section选择器的元素
      article > section { border: 1px solid #ccc }: 选择article下紧跟着子元素命中的section选择器的元素
      header + p { font-size: 1.1em }：用同层相邻组合选择器+选择header元素后紧跟的p元素
      article ~ article { border-top: 1px dashed #ccc }：用同层全体组合选择器~，选择所有跟在article后的同层article元素，不管它们之间隔了多少其他元素
  2-4. 嵌套属性
    nav {
      border: {
      style: solid;
      width: 1px;
      color: #ccc;
      }
    }
    nav {
      border-style: solid;
      border-width: 1px;
      border-color: #ccc;
    }

### 3.导入SASS文件
  sass的@import规则在生成css文件时就把相关文件导入进来。这意味着所有相关的样式被归纳到了同一个css文件中，而无需发起额外的下载请求。
  3-1. 使用SASS部分文件
    当通过@import把sass样式分散到多个文件时，你通常只想生成少数几个css文件。那些专门为@import命令而编写的sass文件，并不需要生成对应的独立css文件，这样的sass文件称为`局部文件`。
    对此，sass有一个特殊的约定来命名这些文件。此约定即，sass局部文件的文件名以下划线开头。这样，sass就不会在编译时单独编译这个文件输出css，而只把这个文件用作导入。
  3-2. 默认变量值
    $fancybox-width: 400px !default;
    .fancybox {
    width: $fancybox-width;
    }
    !default: 如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。
  3-3. 嵌套导入
    sass允许@import命令写在css规则内，这样生成对应的css文件时，局部文件会被直接插入到css规则内导入它的地方。
  3-4. 原生的CSS导入
    把原始的css文件改名为.scss后缀

### 4. 静默注释
  静默注释：其内容不会出现在生成的css文件中。  /* ... */

### 5. 混合器
  通过sass的混合器实现大段样式的重用。
  混合器使用@mixin标识符定义，然后就可以在你的样式表中通过@include来使用这个混合器。
    @mixin rounded-corners {
      -moz-border-radius: 5px;
      -webkit-border-radius: 5px;
      border-radius: 5px;
    }
    notice {
      background-color: green;
      border: 2px solid #00aa00;
      @include rounded-corners;
    }
  5-1. 何时使用混合器
    sass允许把css规则放在混合器中
  5-2. 混合器中的CSS规则

  5-3. 给混合器传参
    @mixin link-colors($normal, $hover, $visited) {
      color: $normal;
      &:hover { color: $hover; }
      &:visited { color: $visited; }
    }
    a {
      @include link-colors(blue, red, green);
    }
  5-4. 默认参数值
    @mixin link-colors(
      $normal,
      $hover: $normal,
      $visited: $normal
    )
    {
      color: $normal;
      &:hover { color: $hover; }
      &:visited { color: $visited; }
    }

### 6. 使用选择器继承来精简CSS
  //通过选择器继承继承样式
  .error {
    border: 1px solid red;
    background-color: #fdd;
  }
  .seriousError {
    @extend .error;
    border-width: 3px;
  }
### 7. 小结
