## 块级元素行内元素
> - 块级(block-level); 行内(内联、inline-level)
> - 块级可以包含块级和行内，行内只能包含文本和行内
> - 块级占据一整行空间，行内占据自身宽度空间
> - 宽高设置、内外边距的差异  

1. 块级元素和行内元素设置边距的差异
块级元素元素可以设置margin、padding上下左右；
行内元素：margin只有左右有效；padding左右有效，但是上下只对背景生效，并且不占据高度. 
用下面的代码加上可以验证这一点，span加上上下padding后如果高度发生变化的话，上下两个div应该会被撑开而不是像现在这样被叠加了。

``` html
<div></div>
 <span>AAAAAA</span>
<div></div>

div{
  width:200px;
  height:300px;
  border:1px solid red;
}

span {
  border: 1px solid green;
  padding: 20px 50px;
  background-color:yellow
}

```
### block-level

```
div h1 h2 h3 h4 h5 h6 p hr
form ul dl ol pre table
li dd dt tr td th
```

### inline-level
```
em strong span a br img 
button iput label select textarea
code script 
```

## 宽高
> 只对**块级元素**设置生效,对行内元素设置无效

```
<style>
  .box {
    width: 200px;
    height: 100px;
    background-color: red;
  }
</style>
<div class="box"></div>
```


## 边框
> border-width, border-color, border-style

```
.box {
  border-width: 1px;
  border-color: red;
  border-style: solid; /* dotted  dash*/
}
/* 简写 */
.box2 {
  border: 1px dotted #ccc;
}
```

### 使用边框做三角形
可以用这个技巧来做tooltip的箭头
```
.t1{
  height:0;
  width:0px;
  border-top:solid 20px red;
  border-right:solid 20px green;  
  border-bottom:solid 20px blue;
  border-left:solid 20px yellow;  
  /*border-left:solid 20px transparent;*/
 /* border-right:solid 20px transparent;*/
}

<div class='t1'></div>
```

## 边距
![](http://7xpvnv.com2.z0.glb.qiniucdn.com/3ee72bf4-b745-4244-bd35-cc207491c36f) 

### padding
> - padding代表内边距，有四个方向的值，可以合写，值可以是数值也可以是百分比（相对于父容器、不是自身）

- padding-top
- padding-right
- padding-bottom
- padding-left

```
padding: 20px; /* padding: 20px 20px 20px 20px; */
padding: 10px 20px; /* padding: 10px 20px 10px 20px; */
padding: 10px 20px 30px; /* padding: 10px 20px 30px 20px; */
```
### margin
> - margin 代表外边距，有四个方向的值，可以合写，值可以是数值也可以是百分比（相对于父容器、不是自身）。还可以是负值
> - 外边距合并问题

- margin-top
- margin-right
- margin-bottom
- margin-left

```
margin: 20px; /* margin: 20px 20px 20px 20px; */
margin: 10px 20px; /* margin: 10px 20px 10px 20px; */
margin: 10px 20px 30px; /* margin: 10px 20px 30px 20px; */
```

### margin: 0 auto
> 对于**块级元素** 设置 `margin: 0 auto` 可达到水平居中目的 。而且块级元素要加固定宽度，否则块级元素会独占一行，何来居中。
```
.box {
  /* margin: 0 auto; 实际上是下面两个起作用 */
  margin-left: auto;
  margin-right: auto;
}

<div style="height: 20px; border: 1px solid red; margin: 0 auto;"></div>
```


###  {margin: 0; padding: 0}
去除元素默认 margin padding

## display
display可以用来改变元素的特性。把块级元素变成行内元素，或者把行内元素变成块级元素。
> - 块级：block, list-item, table
> - 行内: inline, inline-table, inline-block

```
display
Value:    inline | block | list-item | inline-block | table | inline-table | table-row | table-cell | none | inherit
Initial:    inline
Applies to:   all elements
Inherited:    no
```

## font
- font-size：字体大小
- font-family：字体，宋体、微软雅黑、Arial等。可以写多个，用逗号分隔
- font-weight：文字粗度，常用的就是默认值regular和粗体bold
- line-height：行高，可以用百分比、倍数或者固定尺寸
  > 倍数、百分都是相对于元素自身的字体大小。最好的方法是使用倍数来设置line-height. 使用百分比、em等单位都会出现意想不到的后果
https://developer.mozilla.org/en-US/docs/Web/CSS/line-height
  
- 以上属性都可继承给子元素 

font-family,font-weight并不是设置任何值都有的。font-family要看用户的电脑中有无设置的字体，没有的话浏览器会自己猜；font-weight要看字体库中有没有设计对应的文字粗度；

### 用法
下面两种写法是等价的
```
body{
  font: 12px/1.5 tahoma,arial,'Hiragino Sans GB','\5b8b\4f53',sans-serif;
}

p {
  line-height: 1.5;
  font-size: 12px;
  font-family: 'Helvetica Neue',Helvetica,Arial,sans-serif;
  font-weight: bold;
}
```

### font-family
> 使用浏览器打开页面时，浏览器会读取 HTML 文件进行解析渲染。当读到文字时会转换成对应的 unicode码（可以认为是世界上任意一种文字的特定编号）。再根据HTML 里设置的 font-family （如果没设置则使用浏览器默认设置）去查找电脑里（如果有自定义字体@font-face ，则加载对应字体文件）对应字体的字体文件。找到文件后根据 unicode 码去查找绘制外形，找到后绘制到页面上

[字体的原理](https://zhuanlan.zhihu.com/p/22724856)

### font-family的写法
> 在 CSS 中设置字体时，直接写字体中文或英文名称浏览器都能识别，直接写中文的情况下编码（GB2312、UTF-8 等）不匹配时会产生乱码。保险的方式是将字体名称用Unicode来表示

宋体 | SimSun | \5B8B\4F53
黑体 | SimHei | \9ED1\4F53
微软雅黑 | Microsoft YaHei | \5FAE\8F6F\96C5\9ED1

打开控制台 `escape('微软雅黑')`，把 `%u`替换成 `\`

### Chrome最小字体
chrome 默认字体大小16px, 最小字体 12px [解决方案](https://github.com/islittle/Web-Developer/blob/master/css-notes/compatible-with-less-than-12px-fontsize.md)

## 文本

- text-align：文本对其方式 left、center、right、justify
- text-indent：文案第一行缩进距离
- text-decoration： none、underline、line-through、overline
- color：文字颜色
- text-transform：改变文字大小写none、uppercase、lowercase、capitalize
- word-spacing：可以改变字（单词）之间的标准间隔
- letter-spacing：字母间隔修改的是字符或字母之间的间隔

[范例](http://js.jirengu.com/riyu/1/edit)

### 行内元素水平居中
在父元素上使用text-align:center
```
<style>
  .box{
    text-align: center;
  }
</style>
<div class="box">
  <img src="http://7xpvnv.com2.z0.glb.qiniucdn.com/6673909a-098c-4202-8b80-f4aad64e0262.png">
</div>
```

### 单行文本溢出加...

```
.card > h3{
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
```
[demo](http://js.jirengu.com/dode/1/edit)

## 颜色
- 单词: `red`, `blue`, `pink`, `yellow`, `white`, `black`
- 十六进制: `#000000`, `#fff`, `#eee`, `#ccc`, `#666`, `#333`, `#f00`, `#0f0`, `#00f`
- rgb: `rgb(255, 255, 255)`, `rgb(0, 255, 0)`
- rgba: `rgba(0,0,0,0.5)`
- [更多](http://www.w3schools.com/colors/default.asp)


## 单位
- px： 固定单位
- 百分比(宽高？文字大小？line-height? position?)
 1. 宽高： 相对于父元素的宽高。块级元素不要随便写width：100%, 因为块级元素本来就是撑满一行的，加了反而会有其他问题。
 2. line-height: 相对于**自身字体**大小
 3. position: absoluate/fix时，top,left,right,bottom的值： 相对于第一个position非static的父容器的宽高
 4. 对于height来说，如果设置了百分比的话，那么它的父元素必须显示的设置了height。父容器的height的单位可以是px或者百分比
- em: 相对单位，相对于父元素字体大小
 > 有些元素的默认样式就会使用em做单位。如h1的默认样式就有:font-size:2em。如果给h1的父容器设置font-size的话，h1的字体就会发生改变
- rem: 相对单位，相对于根元素(html)字体大小
- vw vh: 相对单位，1vw 为屏幕宽度的1%
  使用这个属性要注意兼容性问题 [兼容性](http://caniuse.com/#search=vw)


## 其他
### a 链接设置颜色
a 有默认颜色和样式，会覆盖继承的样式

```
a {
  color: red;
  text-decoration: none;
}
```
### 列表去掉点

```
ul{
  list-style: none;
}
/*也可这样*/
li{
  list-style: none;
}
```

## 背景
|属性 |描述|
|---|---|
|background|  简写属性，作用是将背景属性设置在一个声明中|
|background-attachment| 背景图像是否固定或者随着页面的其余部分滚动|
|background-color|  设置元素的背景颜色|
|background-image|  把图像设置为背景|
|background-position| 设置背景图像的起始位置|
|background-repeat| 设置背景图像是否及如何重复|
|background-size| 设置背景的大小(兼容性) |

* background-position：默认左上角
  * x y
  * x% y%
  * [top | center | bottom] [left | center | right]

* background-repeat
  * no-repeat：背景图片在规定位置
  * repeat-x：图片横向重复
  * repeat-y：图片纵向重复
  * repeat：全部重复

* background-size
  * `100px 100px`
  * `contain`
  * `cover`
```
background-color: #F00;
background-image: url(background.gif);
background-repeat: no-repeat;
background-attachment: fixed;
background-position: 0 0;
```

可以缩写为一句：

```
background: #f00 url(background.gif) no-repeat fixed 0 0;
```
背景图片要注意两点
1. 窗口（容器）要设置宽高
2. 背景 ，background相关属性的设置

#### 首页满屏
``` css

html{
  height:100%
}

body{
  margin:0;
  height:100%;
}

header{
  border:1px solid red;
  height:100%;
  background: url(https://images.unsplash.com/photo-1485827031228-b1736cb1fd06?ixlib=rb-0.3.5&s=c8c9493d8ba21c730664427ec676c591&auto=format&fit=crop&w=967&q=80) 
    center center no-repeat;
  background-size:cover;
  overflow:hidden;
}

header h1{
  color:#fff;
  text-align:center;
  margin-top:200px
  
}

main{
  border: 1px solid black;
}

footer{
  border: 1px solid grey;
}

 <header>
   <h1>未来已来</h1>
 </header>
  
 <main>
   <p>hello</p>
 </main>
  
  <footer>
    <p>footer</p>
  </footer>
```



### CSS Sprite
- 指将不同的图片/图标合并在一张图上, 然后使用background-position来设置具体的位置
- 使用CSS Sprite 可以减少网络请求，提高网页加载性能。

## 隐藏or透明
- `opacity: 0` ; 透明度为0，整体。依然占据位置
- `visibility: hidden` ;  和opacity:0 类似
- `display:none`; 消失，不占用位置
- `background-color: rgba(0，0，0，0.2)` 只是背景色透明


## inline-block
> - 既呈现 inline 特性(不占据一整行，宽度由内容宽度决定)
> - 又呈现 block 特性 (可设置宽高，内外边距)
> - 缝隙问题
#### inline-block 缝隙问题
很多人认为inline-block产生缝隙是它自己本身造成的，可事实是缝隙是由行内元素造成的，而不是inline-block.

.btn使用了display:inline-block后，btn1,btn2,btn3之间会出现缝隙。但是把display设置为inline后分析并没有消失。
```
<div class="btn">btn1</div>
<div class="btn">btn2</div>
<div class="btn">btn3</div>
.btn{
 border: 1px solid #ccc;
 display: inline-block;
 /*display: inline;*/
 width:50px;
}

```
直接使用span元素也有缝隙，甚至直接写btn11、btn22、btn33也会有缝隙。这就证明了缝隙是由行内元素造成的，而不是inline-block。 inline-block产生缝隙的原因只是因为它带了inline的特性而已。

**行内元素产生缝隙的原因是： 浏览器会把多个空白字符、换行当成一个空白字符**
只要把多个行内元素连着写，不换行就不会出现缝隙里。但是这样的话，html代码的可读性就相当差了，一般不会这么写。
```
<span class="btn">btn1</span>
<span class="btn">btn2</span>
<span class="btn">btn3</span>
btn11
btn22
btn33
.btn{
 border: 1px solid #ccc;
}
```

#### inline-block如何去掉缝隙
因为空白字符本身还是一个字，所以我们可以在父容器上把font-size设置为0来去掉空白字符。然后在需要的地方再把font-size设置回来
```
<div class="ct">
  <div class="btn">btn1</div>
  <div class="btn">btn2</div>
  <div class="btn">btn3</div>
</div>
.ct {
  font-size: 0;
}
.btn{
 border: 1px solid #ccc;
 display: inline-block;
 width:50px;
 font-size: 12px;
}
```

#### inline-block元素之间对齐
使用inline-block时有时会出现元素直接垂直方向不对齐的情况。这个要使用vertical-align:top来让它们之间对齐。
```
<div class="ct">
  <div class="btn">hello world</div>
  <div class="btn">study javascript css html</div>
</div>
.ct {
  font-size: 0;
  border: 1px solid red;
}
.btn{
 border: 1px solid #ccc;
 display: inline-block;
 width:100px;
 font-size: 12px;
 vertical-align:top
}

```

#### inline-block元素水平居中
在父容器上设置text-align:center即可。margin：0 auto是不行的。

## line-height 
> - line-height: 2
> - line-height: 100%
> - height = line-height 来垂直居中单行文本

## 盒模型
#### 标准盒模型
> ![](http://lsly1989.qiniudn.com/201503151.JPG)

#### IE盒模型
> ![](http://lsly1989.qiniudn.com/201503152.JPG)


#### 区别

W3C标准中`padding`、`border`所占的空间不在width、height范围内，大家俗称的IE的盒模型width包括`content尺寸`＋`padding`＋`border`
> ie678怪异模式（不添加 doctype）使用 ie 盒模型，`宽度=边框+padding+内容宽度
![](http://7xpvnv.com2.z0.glb.qiniucdn.com/93138fc5-aec8-4693-9065-5d99dee1f223.jpg)

> `chrome， ie9+, ie678(添加 doctype) 使用标准盒模型， 宽度= 内容宽度`
![](http://7xpvnv.com2.z0.glb.qiniucdn.com/d419e9a6-90c6-4c85-814f-36795e1ee03b.jpg)

#### 使用css3新样式box-sizing
不设置box-sizing属性的话用的都是标准盒模型（context-box）
1.  `box-sizing: content-box`：w3c标准盒模型
2.  `box-sizing: border-box`：“IE盒模型”

**设置`box-sizing：border-box`在水平排列多个元素时特别有用。** 如果不用`box-sizing：border-box`的话计算宽度时就会把width的值再加上border:2px, 这样的话在一行就放不下3个了。

如果把`width:33.33%`改小一点，虽然可以在一行放下3个，但是此时并不是把一行的宽度均分为3份。一旦页面拉大，左右空白的宽度不一致的问题就和明显了。

```
 <div class="ct">
    <div>1</div>
    <div>2</div>
    <div>3</div>
  </div>

.ct>div{
  width:33.33%;
  height:100px;
  display:inline-block;
  font-size:12px;
  border: 2px solid red;
  box-sizing:border-box;
}
```
如果`.ct > div`中设置了marigin属性，如：`margin:10px`的话，宽度就必须使用calc属性来计算了，如: `calc(33.33% - 20px)`; 
