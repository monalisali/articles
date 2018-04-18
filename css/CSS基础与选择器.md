## CSS基础
详细内容可以直接查看课件：
- [CSS基础课件](https://github.com/jirengu/slides/blob/master/CSS-CSS%E5%9F%BA%E7%A1%80.md) 以下列出一些重点
- [CSS选择器](https://github.com/jirengu/slides/blob/master/CSS-CSS%E9%80%89%E6%8B%A9%E5%99%A8.md)

## CSS 是什么
Cascading Style Sheets, 层叠样式表
## 如何在页面引用CSS
1.  内联样式  元素上直接使用style属性
2.  内部样式  在html中的任何地方使用 style标签。一般写在header中
     ```css
        <style type="text/css">
            h1 {
                 color:red;
             }
        </style>
    ```
3. 外部样式表 
- 使用 link标签
``` html
<head>
  <link rel="stylesheet" type="text/css" href="index.css">
</head>
```
- 使用style标签和@import  @import是css语法，所以必须写在style标签或者css文件中
``` html
<style>
  @import url("hello.css");
  @import "world.css";
</style>

```

4. 浏览器默认样式  不可修改

## 认识开发者工具
F12调试

## 文件路径
#### 相对路径
1.  `.`表示当前路径  css/a.css 和 ./css/a.css是等价的
2. `../`表示上级目录

#### 绝对路径
文件在硬盘上的完整路径
css路径不要使用绝对路径，发布时会有问题

#### 网站路径
- `/` 表示站点的根路径

#### 书写规范
- 语法不区分大小写，但建议统一使用小写
- 不使用内联的style属性定义样式
- **不要使用属性样式，太古老了**
- id和class使用有意义的单词，分隔符建议使用 `-`
- 有可能就使用缩写
- 属性值是0的省略单位
- 块内容缩进
- 属性名冒号后面添加一个空格

##### 文件路径
- css路径不要使用绝对路径

##### @charset
- 样式里面使用`@charset "utf-8"` 来告诉浏览器当前css文件使用的是什么编码格式，以防css中一些中文内容出现乱码，如：中文注释


## 选择器
#### 基础选择器
- `*`   通用选择器
- `#id`  布局时的大分区一般用id选择器，其他的用class
- `.class` 
- `element`

#### 组合选择器
- `E,F`
- `E F`
- `E>F`
- `E+F`
- `E~F`

p.active.box 中间没有空格表示同时拥有。这句代码表示:既**包含**class active又包含**class box** 的p元素

p .active 有空格表示所有子元素。这句代码表示p元素中所有拥有class active的子元素

#### 属性选择器
- `E[attr]`
- `E[attr = value]`
- 等等

#### 伪类选择器
把伪类理解成：一个元素拥有某种状态时对它添加样式
可以选择红框中的状态来触发相应的伪类状态
![image.png](http://upload-images.jianshu.io/upload_images/9425951-a2759ad97140d86f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
-  E:link
- `E:hover` 所有元素都可以使用
- `E:visited`
- `E:active`
- `E:focus`
- `E:checked`
- `E:first-child` 父亲的第一个子元素
- `E:last-child`
- `E:first-of-type`  匹配父元素下使用同种标签的第一个子元素，等价于nth-of-type(1)
- `E:nth-of-type(n)` 父亲中同种标签的第n个元素
    n的取值
   - 1，2，3，4，5
   - 2n+1, 2n, 4n-1
   - odd, even

- a链接伪类的正确写法，要注意顺序不能乱。因为有样式覆盖问题
![image.png](http://upload-images.jianshu.io/upload_images/9425951-6778a2188581d8ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- checked，正准备勾选时被触发

#### 伪元素选择器
- `E::first-letter` 第一个字母
- `E::first-line` 第一行
- `E::before` 必须要有content，但content可以为空`''`
- `E::after` 必须要有content，但content可以为空`''`
before , after最大的作用就是实现效果时可以省标签。

#### 选择器优先级
1. 在属性后面使用 !important 会覆盖页面内任何位置定义的元素样式
2. 作为style属性写在元素标签上的内联样式
3. id选择器
4. 类选择器
5. 伪类选择器
6. 属性选择器
7. 标签选择器
8. 通配符选择器
9. 浏览器自定义
10. 继承的样式

复杂场景时可以使用数个数的方式来查看哪个样式高
- 行内样式 <div style="xxx"></div> ==> a
- ID 选择器 ==> b
- 类，属性选择器和伪类选择器 ==> c
- 标签选择器、伪元素 ==> d

**依次看a,b,c,d的个数，a与a相等再看b，以此类推。**
```
*             {}  /* a=0 b=0 c=0 d=0 -> 0,0,0,0 */
p             {}  /* a=0 b=0 c=0 d=1 -> 0,0,0,1 */
a:hover       {}  /* a=0 b=0 c=1 d=1 -> 0,0,1,1 */
ul li         {}  /* a=0 b=0 c=0 d=2 -> 0,0,0,2 */
ul ol+li      {}  /* a=0 b=0 c=0 d=3 -> 0,0,0,3 */
h1+input[type=hidden]{}  /* a=0 b=0 c=1 d=2 -> 0,0,1,2 */
ul ol li.active   {}  /* a=0 b=0 c=1 d=3 -> 0,0,1,3 */
#ct .box p        {}  /* a=0 b=1 c=1 d=1 -> 0,1,1,1 */
div#header:after  {}  /* a=0 b=1 c=0 d=2 -> 0,1,0,2 */
style=""          /* a=1 b=0 c=0 d=0 -> 1,0,0,0 */
```

**如果优先级都一样的话，后面的覆盖前面的**


## 选择器重点
- .class 类选择器是**包含**而不是等于
"aa"和"bb"都会变成红色
```html
.header{
  color:red;
}
<div class="header">aa</div>
<div class="header logo">bb</div>
```   
- 有空格表示所有层级的下级子元素 ； 紧挨着表示即又的关系
`.header .logo` 中间有空格，表示class包含header之元素下所有层级子元素，且子元素class包含logo。所以此时，hello变成红色
`.header.logo `中间没有空格，表示class包含header和logo的元素。所以此时，bb变成红色
```html
.header .logo{
  color:red;
}
  <div class="header">
      <p class="logo">hello</p>
  </div>
  <div class="header logo">bb</div>
```
- 伪类的个数选择器指的不是单个元素，而是所有满足条件的元素中的第几个元素。
` .container :first-child ` : .container后有空格： class包含container之元素的**所有层级**的第一个元素。所以 ` <div class="box">div.box</div>` `<div class="item">div.item</div>` `  <p>zzzz</p>` 会变成红色

  `.container:first-child` .container后没有空格： class包含container之元素，其父元素的第一个child。显然` <div class="container">`没有父元素，这样就没有任何元素会变成红色

  改成`.box:first-child`   .box后没有空格：class包含box之元素，其父元素的第一个child。 有3个元素满足条件`.box`,但第一个元素只有` <div class="box">div.box</div>`

  改成`.box: first-child`   .box后有空格: class包含box之元素，**所有层级**的第一个子元素。 ` <div class="item">div.item</div>` 和  `<p>zzzz</p>`被找到


```html
  <style>
 .container :first-child {
  color: red;
}

 </style>
  <div class="container">
    <div class="box">div.box</div>
    <p class="box">p.box</p>

    <div class="box">
      <div class="item">div.item</div>
      <div>
         <p>zzzz</p>
      </div>
      <p class="item">p.item</p>
    </div>

  </div>
```
