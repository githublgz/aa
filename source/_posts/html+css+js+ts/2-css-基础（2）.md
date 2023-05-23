---

title: css-基础（2）
cover: false
top: false
toc: false
mathjax: false
tags:
  - css
categories:
  - css
abbrlink: 23375
date: 2018-02-25 21:56:54
author:
img:
coverImg:
password:
summary:
keywords:
---
------

#### 第八章 css基础知识

------

- ```
  css基础知识：
  ```

  - `css`样式表的定义
  - `css`：（Cascading Style Sheets）层叠样式表；
  
- 分类及位置：内部样式

  ```
  -head区域
  ```

  ```
  style
  ```
  
  标签里面

  - 外部样式-`link`调用
  - 内联样式-标签元素里面
  
- `css`内的注释：/`*`注释内容`*`/

- `css`样式表的语法

  - `CSS`规则由两个主要的部分构成：要添加样式的盒子名或者标签名、和要添加的样式。

  - 盒子名或者标签名{属性:值;}

  - **CSS中几种颜色的表示方法**

    - 用颜色名表示
      - 有17个预先确定的颜色，它们是
        - `aqua`, `black`, `blue`, `fuchsia`, `gray`, `green`, `lime`, `maroon`, `navy`,
          　　`olive`, `orange,` `purple`, `red`, `silver`, `teal`, `white`, and `yellow`

    - **用十六进制的颜色值表示(红、绿、蓝)**
      - `#FF0000`或者`#F00`
    - **用rgb(r,g,b)函数表示**
      - 如：`rgb(255,255,0)`
    - **用hsl(Hue,Saturation,Lightness)函数表示（色调、饱和度、亮度)**
      - 如：`hsl(120,100%,100%)`,色调0代表红色，`120`代表绿色，`240`代表
        蓝色
    - **用`rgba(r,g,b,a)`函数表示**
      - 其中`a`表示的是改颜色的透明度，取值范围是`0~1`，其中`0`代表完全透明
    - **用hsla(Hue,Saturation,Lightness,alpha)函数表示**
      - 色调、饱和度、亮度、透明度
    - 例子

```
  <div style="position:absolute;top:0px">
	<div style="background-color:gray;">background-color:gray</div>
	<div style="background-color:#F00;">background-color:#F00</div>
	<div style="background-color:#ffff00;">background-color:#ffff00</div>
	<div style="background-color:rgb(255,0,255);">background-color:rgb(255,0,255)</div>
	<div style="background-color:hsl(120,80%,50%);">background-color:hsl(120,80%,50%)</div>
	<div style="background-color:rgba(255,0,255,0.5);">background-color:rgba(255,0,255,0.5)</div>
	<div style="background-color:hsla(120,80%,50%,0.5);">background-color:hsla(120,80%,50%,0.5)</div>
</div>
```

![img](http://upload-images.jianshu.io/upload_images/1480597-39e61a813f637282.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 内部样式表
  - 当单个页面需要设置样式时，就应该使用内部样式表。
  - 使用 ``标签在文档``里面定义内部样式表

```
<head>
 <style type="text/css" >
  p{color:red;}
 </style>
</head>
```

- 从外部引入到样式分为两种：（注意写在`head`标签里面）
- 当样式需要应用于很多页面时，就需要用到外部样式表，首先需要创建一个`css`文件，然后引用到我们的页面中。
- `Link`样式表式： ``
- `Html`式： `@import url("css.css");>`

- 内联样式表（优先级高）
  - 写在标签里面的样式
  - 如：``
- 表示给`p`标签里面的文字颜色设置为红色
- 区别：外链样式与导入样式
  - `link`标签是属于`xhtml`范畴，而`@import`则是`css2.1`中特有的。`link`标签除了可以加载`CSS`外，还可以做很多其它的事情，比如定义`RSS`，定义`rel`连接属性等，`@import`就只能加载`CSS`了。
  - 加载的顺序的区别，`link`加载的`css`时，是一种并行(没有尝试是否是这样)加载`CSS`方式，而`@impor`则在整个页面加载完成后才加载。
  - 兼容性的区别，因`@import``CSS2.1`才特有的，所以对于不兼容`CSS2.1`的浏览器来说，无效。
  - 在样式控制上(比如动态改变网页的布局时,使用`javascript`操作`DOM`)的区别，此时`@import`就无能为力了。

------

- 样式的优先级补充

  - 相同权值情况下，

    ```
    CSS
    ```

    样式的优先级总结来说，就是——就近原则（离被设置元素越近优先级别越高）：

    - `内联样式表（标签内部）` > `嵌入样式表（当前文件中）`> `外部样式表（外部文件中）`

- 权值不同时，浏览器是根据权值来判断使用哪种`css`样式的，哪种样式权值高就使用哪种样式

- 层叠优先级是:

  - `浏览器缺省`< `外部样式表` < `内部样式表` < `内联样式`

- 其中样式表又有:`类选择器` < `类派生选择器`<`ID选择器` < `ID派生选择器`

- 派生选择器以前叫上下文选择器，所以完整的层叠优先级是:

  - `浏览器缺省` <`外部样式表` < `外部样式表类选择器` < `外部样式表类派生选择器`< `外部样式表ID选择器` < `外部样式表ID派生选择器`< `内部样式表` < `内部样式表类选择器` < `内部样式表类派生选择器` < `内部样式表ID选择器` < `内部样式表ID派生选择器` < `内联样式`…共`12`个优先级

- 另外，如果同一个元素在没有其他样式的作用影响下，其`Class`定义了多个并以空格分开，其优先级顺序为：

  - 一个元素同时应用多个`class`，后定义的优先（即近者优先），加上`!important`者最优先！

------

#### 第九章 css选择器(上)

- ```
  css选择器：
  ```

  - `class`类选择器可以重复利用
  - `id`选择器唯一
  
- 标签选择器

  - 什么是选择器：css选择器就是要改变样式的对象

- 选择器`{属性:值;属性:值;}`

- 标签选择器：页面中所有的标签都是一个选择器 `p{color:red;}`

- `ID`选择器

  - 选择`id`命名的元素 以 `#` 开头 `#p1{color:#0f0;}`

- 类选择器

  - `class`选择器，选择`clas`命名的元素 以`.`开头 `.first{color:#00f;}`

- `css`代码写完后上线前要经过压缩处理

- 本地和服务器分两个`css`版本（备份）

- 压缩后注释都清除，空间体积减少

- 群组选择器

  - 选择多个元素,以逗号隔开 `#main,.first,span,a,h1{color:red;}`

- 包含选择器

  - 选择某元素的后代元素，也称后代选择器，父类与子类间以空格隔开p

    - `span{color:red;}`
  
- 属性选择器

  - 选择包含某一属性的元素
  - `a[title]{color:red;}` 选择包含`title`的`a`标签
  - `a[title][href]{color:red;}` 选择包含`title`和`href`的`a`标签

- `>` `+` 选择器子类选择器：只选择子元素（只选择儿子）（相当于包含元素）

  - `p > span{color:red;}`

- 相邻兄弟选择器：只选择后面的相邻兄弟元素

  - `p + span{color:red;}`

------

#### 第十章 css选择器(下)

------

- ```
  <a>伪类选择器
  ```

  - `a:link {color:#FF0000;}` / *未访问的链接* / （只用于a标签）
  - `a:visited {color:#00FF00;}` / *已访问的链接* / （只用于a标签）
  - `a:hover {color:#FF00FF;}`/* 鼠标移动到链接上
  - `*/`（可和其他标签结合一起用）
  - `a:active {color:#0000FF;}` / *选定的链接* /
  - 注意
    - 伪类选择器的排序很重要，`a:link` `a:visited` `a:hover` `a:active`，记作`lvha`
  
- 输入伪类选择器（针对表单）

  - `input:focus{color:red;}` / *键盘输入焦点* /

- 其他伪类选择器

  - `p:first-child{color:red;}` /`* 第一个p *`/
  - `:before` 在元素之前添加内容。
  - `:after` 在元素之后添加内容。

- `css`优先规则

  - 内联样式表-> `ID` 选择器—> `Class` 类选择器->标签选择器

------

#### 第十一章 背景属性

------

- 背景属性：

  - 背景的添加 ：

  - 背景颜色的添加:

    - `background:red;`
    - `backgronnd-color:red;`

  - 背景图片的添加：

    - `background:url(“images/1.jpg”);`
    - `backgronnd-image:url(“images/1.jpg”);`

  - 背景的平铺

  - 什么是平铺？平铺就是图片是否重复出现

    - 不平铺：`background-repeat:no-repeat;`
    - 水平方向平铺：`background-repeat:repeat-x;`
    - 垂直方向平铺：`background-repeat:repeat-y;`
    - 完全平铺：默认为完全平铺

  - 背景图片的定位

    - 背景图片的定位就是可以设置显示背景图片的位置，通过属性`background-position`来实现
    - `background-position`的取值可为英文单词或者数值和百分值。
    - `background-positon`的英文单词取值
    - `top left`
    - `top center`
    - `top right`
    - `center left`
    - `center center`
    - `center right`
    - `bottom left`
    - `bottom center`
    - `ottom right`

  - ```
    background-positon
    ```

    的数值取值

    - `background-position:x y;`

  - ```
    positon
    ```

    的百分值取值

    - `background-position:x% y%;`

  - 背景图片的大小

    - 背景图片的大小可以通过属性`background-size`来设置`background-size`的取值可为数值和百分值。

  - `background-size`的数值取值

    - `background-size:x y;`

  - `background-size`的数值取值

    - `background-size:x% y%;`

  - 背景图片的滚动

    - 背景图片是否随着内容的滚动而滚动由`background-attachment`设置
    - `background-attachment:fixed;` 固定，不随内容的滚动而滚动
    - `background-attachment:scroll;` 滚动，随内容的滚动而滚动

------

#### 第十二章 文字文本属性

------

- `css`文字文本属性：
  - **文字属性**
    - `color:red;` 文字颜色
    - `font-size:12px`; 文字大小
    - `font-weight:“bold”` 文字粗细(`bold/normal`)
    - `font-family:“宋体”` 文字字体
    - `font-variant:small-caps`小写字母以大写字母显示

- **文本属性**

  - `text-align:center;` 文本对齐(`right`/`left`/`center`)

  - `line-height:10px;` 行间距(可通过它实现文本的垂直居中)

  - `text-indent:20px;` 首行缩进

  - ```
    text-decoration:none;
    ```

    - 文本线(`none`/`underline`/`overline`/`line-through`)

  - `letter-spacing`: 字间距

------

#### 第十三章 盒子模型

------

- 盒子模型

  - 盒子模型就是一个有高度和宽度的矩形区域
  - 所有`html`标签都是盒子模型
  - `div`标签自定义盒子模型

- 所有的标签都是盒子模型

  - `class`和`id`的主要差别是：`class`用于元素组（类似的元素，或者可以理解为某一类元素），而`id`用于标识单独的唯一的元素。

- **盒子模型的组成**

  - 盒子模型组成部分：
    - 自身内容：`width`、h`eight` 宽高
    - 内边距： `padding`
    - 盒子边框： `border` 边框线
    - 与其他盒子距离： `margin`外边距
    - 内容+内边距+边框+外边距=面积

- `border` 边框

  - 常见写法 `border:1px solid #f00;`

- 单独属性：

  - `border-width`:

  - ```
    border-style:
    ```

    - `dotted` 点状虚线
    - `dashed`（虚线）
    - `solid`（实线）
    - `double`（双实线）

  - `border-color` (颜色)

- `padding` 内边距

  - 值：`像素`/`厘米`等长度单位、百分比
    - `padding:10px;` 上下左右
    - `padding:10px 10px;` 上下 左右
    - `padding:10px 10px 10px;` 上 左右 下
    - `padding:10px 10px 10px 10px;` 上 右 下 左（设置4个点–>顺时针方向）

- 单独属性：

  - `padding-top:`
  - `padding-right:`
  - `padding-bottom:`
  - `padding-left:`

- 当设置内边距的时候会把盒子撑大，为了保持盒子原来的大小，应该高度和宽度进行减小，根据`width`和`height`减小

- margin 外边距

  - 值：与`padding`相同
  - 单独属性：与`padding`相同

- 外边距合并：两个盒子同时设置了外边距，会进行一个外边距合并

------

**补充盒子模型内容**

------

- **标准盒子模型**
  - 盒子模型是`css`中一个重要的概念，理解了盒子模型才能更好的排版。其实盒子模型有两种，分别是 `ie`盒子模型和标准 `w3c` 盒子模型。他们对盒子模型的解释各不相同，先来看看我们熟知的标准盒子模型

![img](http://upload-images.jianshu.io/upload_images/1480597-320bad065d62c499.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 从上图可以看到标准 `w3c` 盒子模型的范围包括 `margin`、`border`、`padding`、`content`，并且 `content`部分不包含其他部分
- **IE盒子模型**

![img](http://upload-images.jianshu.io/upload_images/1480597-693242e2f03506f8.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 从上图可以看到 `ie`盒子模型的范围也包括 `margin`、`border`、`padding`、`content`
- 和标准 `w3c` 盒子模型不同的是：`ie` 盒子模型的 `content` 部分包含了 `border`和 `padding`
- `IE`盒子模型`width` = `padding`+`border`+`内容`
- 标准盒子模型 = 内容的宽度（不包含`border`+`padding`）
- 例：
  - 一个盒子的 `margin`为 20px，`border` 为 1px，`padding`为 10px，`content` 的宽为 200px、高为 50px，假如用标准 `w3c` 盒子模型解释，那么这个盒子需要占据的位置为：宽 `20*2+1*2+10*2+200=262px`、高 `20*2+1*2*10*2+50=112px`，盒子的实际大小为：宽 `1*2+10*2+200=222px`、高 `1*2+10*2+50=72px`；假如用ie 盒子模型，那么这个盒子需要占据的位置为：宽 `20*2+200=240px`、高 `20*2+50=70px`，盒子的实际大小为：宽 `200px`、高 `50px`
- 那应该选择哪中盒子模型呢？当然是“标准 `w3c` 盒子模型”了。怎么样才算是选择了“标准 `w3c`盒子模型”呢？很简单，就是在网页的顶部加上 `doctype` 声明。
- 假如不加`doctype` 声明，那么各个浏览器会根据自己的行为去理解网页，即 `ie`浏览器会采用 `ie` 盒子模型去解释你的盒子，而 `ff`会采用标准`w3c` 盒子模型解释你的盒子，所以网页在不同的浏览器中就显示的不一样了。
- 反之，假如加上了 `doctype` 声明，那么所有浏览器都会采用标准 `w3c`盒子模型去解释你的盒子，网页就能在各个浏览器中显示一致了。

------

- 用 `jquery` 做的例子来证实一下

```
<html>
<head>
<title>你用的盒子模型是？</title>
<script language="javascript" src="jquery.min.js"></script>
<script language="javascript">
var sbox = $.boxmodel ? "标准w3c":"ie";
document.write("您的页面目前支持："+sbox+"盒子模型");
</script>
</head>
<body>
</body>
</html>
```

- 　上面的代码没有加上 `doctype` 声明，在 `ie` 浏览器中显示 `ie`盒子模型，在 ff 浏览器中显示“标准`w3c` 盒子模型”。

```
<!doctype html public "-//w3c//dtd xhtml 1.0 transitional//en" "http://www.w3.org/tr/xhtml1/dtd/xhtml1-transitional.dtd">
<html>
<head>
<title>你用的盒子模型是标准w3c盒子模型</title>
<script language="javascript" src="jquery.min.js"></script>
<script language="javascript">
var sbox = $.boxmodel ? "标准w3c":"ie";
document.write("您的页面目前支持："+sbox+"盒子模型");
</script>
</head>
<body>
</body>
</html>
```

- 　代码2 与代码1 唯一的不同的就是顶部加了 `doctype`声明。在所有浏览器中都显示“标准 `w3c`盒子模型”
- 所以为了让网页能兼容各个浏览器，让我们用标准 `w3c` 盒子模型

------

#### 第十四章 块元素、行元素与溢出

------

- 基本概念
  - 块级元素：默认情况下独占一行的元素，可控制宽高、上下边距；
  - 行内元素：默认情况下一行可以摆放多个的元素，不可控制宽高和上下边距
- 行块转换
  - `display:none`; 不显示
  - `display:block`; 变成块级元素
  - `display:inline`; 变成行级元素
  - `display:inline-block`; 以块级元素样式展示，以行级元素样式排列
- 溢出
  - `overflow:hidden`; 溢出隐藏
  - `overflow:scroll`; 内容会被修剪，浏览器会显示滚动条
  - `overflow:auto`; 如果内容被修剪，则产生滚动条
- 文本不换行：`white-space:nowrap`;
- 长单词换行：`word-wrap:break-word`;

- 行内元素和快级元素小结
  - 一、**块级元素**：block element
    - 每个块级元素默认占一行高度，一行内添加一个块级元素后无法一般无法添加其他元素（`float`浮动后除外）。两个块级元素连续编辑时，会在页面自动换行显示。块级元素一般可嵌套块级元素或行内元素；
    - 块级元素一般作为容器出现，用来组织结构，但并不全是如此。有些块级元素，如只能包含块级元素。
    - `DIV` 是最常用的块级元素，元素样式的`display:block`都是块级元素。它们总是以一个块的形式表现出来，并且跟同级的兄弟块依次竖直排列，左右撑满。
  - 二、**行内元素**：inline element
    - 也叫内联元素、内嵌元素等；行内元素一般都是基于语义级(semantic)的基本元素，只能容纳文本或其他内联元素，常见内联元素 “a”。比如 `SPAN`元素，`IFRAME`元素和元素样式的`display : inline`的都是行内元素。例如文字这类元素，各个字母 之间横向排列，到最右端自动折行。
  - 三、**block（块）元素的特点:**
    - ①、总是在新行上开始；
    - ②、高度，行高以及外边距和内边距都可控制；
    - ③、宽度缺省是它的容器的100%，除非设定一个宽度。
    - ④、它可以容纳内联元素和其他块元素
  - 四、**inline元素的特点**
    - ①、和其他元素都在一行上；
    - ②、高，行高及外边距和内边距不可改变；
    - ③、宽度就是它的文字或图片的宽度，不可改变
    - ④、内联元素只能容纳文本或者其他内联元素
  - **对行内元素，需要注意如下**:
    - 设置宽度`width` 无效。 设置高度`height`无效，可以通过`line-height`来设置。 设置`margin`
    - 只有左右`margin`有效，上下无效。
    - 设置`padding`只有左右`padding`有效，上下则无效。注意元素范围是增大了，但是对元素周围的内容是没影响的。
  - 五、**常见的块状元素**
    - `address` – 地址
    - `blockquote` – 块引用
    - `center` – 举中对齐块
    - `dir` – 目录列表
    - `div` – 常用块级容易，也是`CSS layout`的主要标签
    - `dl` – 定义列表
    - `fieldset` – `form`控制组
    - `form` – 交互表单
    - `h1` – 大标题
    - `h2` – 副标题
    - `h3` – 3级标题
    - `h4` – 4级标题
    - `h5` – 5级标题
    - `h6` – 6级标题
    - `hr` – 水平分隔线
    - `isindex` – `input prompt`
    - `menu` – 菜单列表
    - `noframes` – `frames`可选内容，（对于不支持frame的浏览器显示此区块内容
    - `noscript` – 可选脚本内容（对于不支持`script`的浏览器显示此内容）
    - `ol` – 有序表单
    - `p` – 段落
    - `pre` – 格式化文本
    - `table` – 表格
    - `ul` – 无序列表
  - 六、**常见的内联元素**
    - `a` – 锚点
    - `abbr` – 缩写
    - `acronym` – 首字
    - `b` – 粗体(不推荐)
    - `bdo` – `bidi override`
    - `big` – 大字体
    - `br` – 换行
    - `cite` – 引用
    - `code` – 计算机代码(在引用源码的时候需要)
    - `dfn` – 定义字段
    - `em` – 强调
    - `font` – 字体设定(不推荐)
    - `i` – 斜体
    - `img` – 图片
    - `input` – 输入框
    - `kbd` – 定义键盘文本
    - `label` – 表格标签
    - `q` – 短引用
    - `s` – 中划线(不推荐)
    - `samp` – 定义范例计算机代码
    - `select` – 项目选择
    - `small` – 小字体文本
    - `span` – 常用内联容器，定义文本内区块
    - `strike` – 中划线
    - `strong` – 粗体强调
    - `sub` – 下标
    - `sup` – 上标
    - `textarea` – 多行文本输入框
    - `tt` – 电传文本
    - `u` – 下划线
  - 七，**可变元素**
    - 可变元素为根据上下文语境决定该元素为块元素或者内联元素。
    - `applet` - `java applet`
    - `button` - 按钮
    - `del`- 删除文本
    - `iframe` - `inline frame`
    - `ins` - 插入的文本
    - `map` - 图片区块(`map`)
    - `object` - `object`对象
    - `script` - 客户端脚本
  - 八、**行内元素与块级元素有什么不同**
    - 区别一：
      - 块级：块级元素会独占一行，默认情况下宽度自动填满其父元素宽度
      - 行内：行内元素不会独占一行，相邻的行内元素会排在同一行。其宽度随内容的变化而变化。
    - 区别二：
      - 块级：块级元素可以设置宽高
      - 行内：行内元素不可以设置宽高
    - 区别三：
      - 块级：块级元素可以设置`margin`，`padding`
      - 行内：行内元素水平方向的`margin-left;` `margin-right;`
    - `padding-left;` `padding-right`;可以生效。但是竖直方向的`margin-bottom`; `margin-top`; `padding-top`; `padding-bottom`;却不能生效。
    - 区别四：
      - 块级：`display:block`;
      - 行内：`display:inline`;
  - 替换元素有如下：（和`img`一样的设置方法）
    - ``、``、``、``
    - ``都是替换元素，这些元素都没有实际的内容
- 可以通过修改`display`属性来切换块级元素和行内元素

------

#### 第十五章 定位

------

- `static`静态定位（不对它的位置进行改变，在哪里就在那里）
  - 默认值。没有定位，元素出现在正常的流中（忽略 `top`,`bottom,` `left, right` 或者 `z-index` 声明）。
- `fixed`固定定位（参照物–浏览器窗口）—做 弹窗广告用到
  - 生成固定定位的元素，相对于浏览器窗口进行定位。 元素的位置通过 `"left"`, `"top"`, `"right"`以及 `"bottom"`属性进行规定。
- `relative`（相对定位 ）（参照物以他本身）
  - 生成相对定位的元素，相对于其正常位置进行定位。
- `absolute`（绝对定位）(除了`static`都可以，找到参照物–>与它最近的已经有定位的父元素进行定位)
  - 生成绝对定位的元素，相对于 `static` 定位以外的第一个父元素进行定位。
  - 元素的位置通过 “`left"`, `"top"`, `"right"` 以及 `"bottom"` 属性进行规定
- z-index
  - `z-index` 属性设置元素的堆叠顺序。拥有更高堆叠顺序的元素总是会处于堆叠顺序较低的元素的前面。
  - 定位的基本思想: 它允许你定义元素框相对于其正常位置应该出现的位置，或者相对于父元素、另一个元素甚至浏览器窗口本身的位置。
- 一切皆为框
  - 块级元素: `div`、`h1`或`p`元素 即：显示为一块内容称之为 “块框“ ;
  - 行内元素: `span`,`strong`,`a`等元素 即：内容显示在行中称 “行内框”;
  - 使用display属性改变成框的类型 即：`display:block`; 让行内元素设置为块级元素，`display:none;` 没有框
- 相对定位：
  - 如果对一个元素进行相对定位，它将出现在它所在的位置上。
  - 通过设置垂直或水平位置，让这个元素“相对于”它的起点进行移动
  - `.adv_relative { position: relative; left: 30px; top: 20px; }`
- 绝对定位：
  - 元素的位置相对于最近的已定位祖先元素，如果元素没有已定位 的祖先元素，它的位置相对于最初的包含块。 `.adv_absolute { position: absolute; left: 30px; top: 20px; }`

------

![img](http://upload-images.jianshu.io/upload_images/1480597-f72c1c8486445df1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![img](http://upload-images.jianshu.io/upload_images/1480597-7ab9cda0bbd7e62f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

------

#### 第十六章 框架

------

- frameset框架：
  - `` —- 用来定义一个框架；双标签
    不能和 `` 一起使用

- `rows`、`cols`属性

  - `rows` 定义行表示框架有多少行（取值 `px`/`%`/ `*` ）
  - `cols` 定义列表示框架有多少列（取值`px`/ `%`/ `*` ）

- frame子框架

  - <`frame`> —- 表示框架中的某一个部分；单标签，要跟结束标志
    - `src` 显示的网页的路径
    - `name` 框架名
    - `frameborder` 边框线（取值 0 / 1）
  - <`noframes`>属性
  - <`noframes`> 提供不支持框架的浏览器显示`body`的内容；双标签

```
<frameset>
     <frame  src=“”  />
     <frame  src=“” />
     <frame  src=“” />
     <noframes>
      <body>内容</body>
     </noframes>
</frameset>
```

- <iframe>内联框架
  
  - `iframe`元素会创建包含另外一个文档的内联框架（即行内框架）
  - 允许和 `body` 一起使用
  - `width` 宽（取值 px / %）
  - `height` 高（取值 px / %）
  - `name` 框架名
  - `frameborder` 边框线（取值 0 / 1）
  - `src` 显示的网页的路径

------

#### 第十七章 css高级属性

------

- opacity透明属性
  
  - ```
    opacity
    ```

    - 对于`IE6/7/`，使用`filter:alpha(opacity:值;`) 值为`0-100`
    - 对于`Webkit`，`Opera`，`Firefox`，`IE9+`，使用`opacity`:值; 值为`0-1`
    - 对于早期火狐，使用`-moz-opacity`:值; 值为`0-1`
    - 所以写透明属性时，一般写法是

```
 {	
    opacity:0.5;
   filter:alpha(opacity：50);/*0-100*/
   -moz-opacity:0.5;	/*取值0-1*/-->针对早起版本的火狐兼容问题的解决
}
```

- `border-radius`圆角边框属性

  - 向div元素添加圆角边框

    - `border-radius:10px`;
- `box-shadow`阴影属性

  - `box-shadow`属性向框添加阴影效果,后面跟4个参数。
  - `box-shadow:0px 0px 10px #000;`
- 属性

  - 是`HTML5`中新增的标签,媒体嵌入插件标签，可以通过``插入音频或视频
  - 格式`.mid` `.wav` `.mp3`等

------

- **CSS部分导图总结**

------

![img](http://upload-images.jianshu.io/upload_images/1480597-4b55b5085a7d0c56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
