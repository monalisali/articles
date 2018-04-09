  

## 浮动
 ##### 什么是浮动
> 一个浮动盒会向左或者向右移动，直到外边缘挨到包含块边沿或者另一个浮动盒的外边。 如果存在行盒，浮动盒的外top（边）会与当前行盒的top（边）对齐。如果没有足够的空间来浮动，它会向下移动，直到空间合适或者不会再出现其他浮动了。
因为浮动盒不在普通流内，在浮动盒之前或者之后创建的为定位的块盒会竖直排列，就像浮动不存在一样。然而，为了给浮动盒的margin box让出空间，接着浮动盒创建的当前及后续行盒会进行必要的缩短。

> 浮动模型也是一种可视化格式模型，浮动的框可以左右移动（根据float属性值而定），直到它的外边缘碰到包含框或者另一个浮动元素的框的边缘。浮动元素不在文档的普通流中，文档的普通流中的元素表现的就像浮动元素不存在一样.

##### 为什么需要浮动
因为html在布局时时常需要打破盒模型的限制，做到左右布局。因为盒模型中常常用到div，而div是块级元素。块级元素是独占一行的，几个div是做不到水平排列布局的，这时就要用到浮动了。

##### 什么是普通文档流
浏览器默认的布局方式和解析方式

**普通文档流：浏览器在渲染页面时会遵循一些原则**
- 块级元素占据一行
- 行内元素水平并排，宽度等于自身内容的宽度
- 块级元素依次向下排列
- 块级元素和行内元素会把父容器撑开

### 浮动的特性
##### 1. 放不下会换行
对应了概念中的： 如果浮动元素没有足够的空间，那么它会向下移动。
```html
<div style="border: solid 2px; width:280px; height: 300px; margin:100px">
      <div style="height: 100px; width: 100px; background-color: Red;  float:left;">
      </div>
      <div style="height: 100px; width: 100px; background-color: Green;  float:left;">
      </div>
      <div style="height: 100px; width: 100px; background-color: Yellow;  float:left;">
      </div>
  </div>
```
![image.png](http://upload-images.jianshu.io/upload_images/9425951-31c611d0f8d46c86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 2. 被卡住
对应了概念中的：一个浮动元素会向左或者向右移动，直到外边缘挨到包含块边沿或者另一个浮动盒的外边。这个**外边**是上、下、左、右四个外边而不仅仅是左右。
黄色div在移动时碰到了红色div的下边框，就卡住了。
 ```html
<div style="border: solid 2px; width:250px;height:300px">
      <div style="height: 120px; width: 100px; background-color: Red;  float:left;">
      </div>
      <div style="height: 100px; width: 100px; background-color: Green;  float:left;">
      </div>
      <div style="height: 100px; width: 100px; background-color: Yellow;  float:left;">
      </div>
  </div>
```
![image.png](http://upload-images.jianshu.io/upload_images/9425951-b359aa3a912e2d9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### 3. 块级元素中的行内元素是可以发现浮动 （非常重要）
本例中，p标签不是浮动元素，它是普通流的一部分，所以它会当浮动元素不存在一样。但是p中的行内元素（文字就是一种匿名的行内元素）还是可以发现浮动元素的。
从效果上看，貌似p发现了浮动元素从而被卡住了。但其实并不是这样的，我们把浮动元素的透明度设为0.2就可以看见p的背景色了。这就说明，p并发现浮动元素，依然被浮动元素遮挡了。但是p中的行内元素的**文字**，是可以发现浮动元素的，因此文字被div给挡住了

```html
   <div style="border: solid 2px; width:280px; height: 300px; margin:100px">
      <div style="height: 100px; width: 100px; background-color: Red;  float:left; opacity:0.2">
      </div>
      <p style="background-color:yellow;margin:0">
        浮动会让元素脱离普通流, 如果浮动的元素后面有一个文档流中元素(即：非浮动元素或者为定位的元素)
        ,那么这个元素的框会表现的像浮动元素不存在一样。
        但是该元素中的文本内容会受到浮动元素的影响,会移动以留出空间。
        用术语说就是浮动元素旁边的行框被缩短,从而给浮动元素流出空间,因而行框围绕浮动框
     </p>
  </div>
```
![image.png](http://upload-images.jianshu.io/upload_images/9425951-3d71080f2e232785.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#####  4. 浮动元素脱离普通文档流
脱离普通文档流的具体表现是
- 普通文档流中的块级元素发现不了浮动元素，就当它不存在一样
- 父容器在计算宽高时发现不了浮动元素。

下例中，红色块级元素没有设置浮动，自动把父容器撑开。蓝色div设置为浮动元素后，父容器（绿色div）在计算宽高时忽略了浮动元素，从而**导致高度塌陷**
``` html
 <div style="border: solid 2px; width:250px;height:300px">
  <div style="border:solid 2px #0e0; margin:10px" >
    <div style="height: 120px; width: 100px; background-color: Red;">   
    </div>
  </div>
  
  
    <div style="border:solid 2px #0e0; margin:10px" >
    <div style="height: 120px; width: 100px; background-color: blue; float:left">   
    </div>
  </div>
   
  </div>
```
![image.png](http://upload-images.jianshu.io/upload_images/9425951-26774f8bea90d26a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

提示
- 注意和absolute定位的不同

##### 5. 块级元素浮动后宽度收缩
块级元素是独占一行的，即使它的内容很短它依然独占一行,**它会自动撑满父级元素**  但是一旦块级元素设置了浮动，那么它就呈现出行内元素的特性：它的宽度就是内容的宽度，除非给它设置宽度
``` html
 <div style="border: solid 2px; width:280px; height: 100px; margin:100px">
      
      <div style="background-color: Red;">
         helloworld
      </div>
     
      <div style="background-color: blue; margin:10px; float: left">
         helloworld
      </div>
  </div>
``` 
![image.png](http://upload-images.jianshu.io/upload_images/9425951-9abfc7ca38fa7ebd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 6. 行内元素浮动后以块级元素特性呈现
行内元素的宽高就是它自身内容的宽度，即使设置了width，height属性也是没用的。要让行内元素可以设置宽高就必须使用`display:inline-block` 
但是，一旦行内元素设置了浮动，就自动有了inline-block一样的特性：可以设置宽高了。

```html
 <div style="border:solid 2px; width:280px; height: 100px;">
     <span style="background-color: Red; width:200px;height:50px">
         helloworld
      </span>
   
      <span style="background-color: blue; margin:10px;width:200px; height:50px;
                   float:left">
         helloworld
      </span>
  
  </div>
```
![image.png](http://upload-images.jianshu.io/upload_images/9425951-c575b14c2aa26412.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 浮动的副作用与解决方法
- 对后续元素位置产生影响 [demo](http://js.jirengu.com/sado/1/edit)
- 父容器高度计算出现问题 [demo](http://js.jirengu.com/towu/1/edit?html,output)

导致上面两个问题的原因是：子元素设置为浮动后，父元素发现不了它，从而在计算父容器高度时忽略了它们。导致了高度塌陷的问题。

解决方法
1. 父容器使用伪类添加一个普通块级元素
使用伪类为父容器加上一个块级元素，并使用`clear:both`来清除浮动。
`clear:both`的字面意思是前后不能有浮动元素。但是需要注意的是，如果前面有浮元素，`clear:both` **并不会** 清除前面元素的浮动。而是把当前元素换到下一行，这样就能保证**前面**没有浮动元素了。我们正是利用这点来解决浮动元素的父容器高度塌陷的问题的。
**具体的做法就是：使用伪类创建一个块级元素，并使用`clear:both` 清除前后的浮动**。虽然父容器不能发现浮动元素，但是可以发现普通元素。`clear:both`属性会把这个伪类中的块级元素放到浮动元素的下一行，这样就把父容器给撑开了。**这种写法是通用的，所有浮动元素的父容器都应该使用这个方法来解决高度塌陷的问题**

我们可以写一个通用的class给所有有需要的父容器使用。这也是最推荐的一种方法。
```  css
.clearfix::after{
      content:'';
      display: block;
      clear: both;
   }
```
兼容IE6,7的写法
```css
 .clearfix{
    *zoom:1
}
```

2. BFC清除浮动
在父容器上设置以下设置属性也可以清除浮动。
`overflow:hidden（不等于visible的几个值都可以）` `display:inline-block`  `position:absoluate` `float:left（浮动本身也是不用清除浮动的）`

## 用 inline-block 还是 浮动
一般来说，大块区域的布局建议用浮动，小块区域的水平布局用inline-block。但也不是绝对的，因为inline-block有缝隙问题需要解决。还是要看具体情况。

##### 用浮动还是定位
##### 浮动和负边距






## BFC
