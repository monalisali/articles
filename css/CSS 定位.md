## 定位
##### 1. 基本属性
> ![image.png](http://upload-images.jianshu.io/upload_images/9425951-9022c77b74a115d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 2. 定位机制
CSS有三种基本的定位机制：普通流，浮动，绝对定位(absolute,fixed)
- 普通流是默认定位方式，在普通流中元素框的位置由元素在html中的位置决定，这也是我们最常见的方式，其中`position: static`与`position:relative`属于普通流的定位方式
- 浮动定位
- 绝对定位包括 absolute和 fixed

##### 3. Position的几种定位方式
###### 3.1 Position: static
以普通文档流的方式进行定位。唯一的作用就是覆盖Postion的其它几种定位方式
###### 3.2 Position: relative 相对定位
相对定位可以看作特殊的普通流定位，元素位置是相对于它在普通流中位置发生变化。元素依然在普通流中。 
下例中的div 2 相对于自己本身在普通流中的位置发生了移动。同时，div2并没有脱离普通文档流，div3依然是可以发现它的。
``` 
.ct{
  width: 200px;
  height: 200px;
  border: 1px solid red;
  
}

.box { 
   width:100px;
   height:50px;
   background-color:red;
}

.ct > div:nth-of-type(2){
  background-color:green;
  position:relative;
  top: 10px;
  left:10px;
}

   <h1>hello world</h1>
   <div class='ct'>
     <div class='box'>1</div>
     <div class='box'>2</div>
     <div class='box'>3</div>     
   </div>
```
![image.png](http://upload-images.jianshu.io/upload_images/9425951-f91a50a0dd657f93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 3.3 Position: absoluate /  fix  绝对定位
- 绝对定位使元素的位置与文档流无关，也**不占据文档流空间**，普通流中的元素布局看到绝对定位元素就像不存在一样
- 绝对定位的元素的位置是相对于距离**最近的非static祖先元素**位置决定的。如果元素没有已定位的祖先元素，那么他的位置就相对于初始包含块html来定位
- 因为绝对定位与文档流无关，所以绝对定位的元素可以**覆盖** 页面上的其他元素，可以通过z-index属性控制叠放顺序，z-index越高，元素位置越靠上


###### 3.3.1 绝对定位时父容器设置相对定位
使用绝对定位时一般都会把它的父容器设置为`position:relative`。否则，元素就会一层层找上去，直到html标签
下例中，如果`.ct`中没有设置position:relative的话。那么，div 2就会以html为参考点而移动到hello world附近了
```
.ct{
  width: 200px;
  height: 200px;
  border: 1px solid red;
  position:relative;
}

.box { 
   width:100px;
   height:50px;
   background-color:red;
}

.ct > div:nth-of-type(2){
  position:absolute;
  background-color:green;
  top: 10px;
  left:10px;
}

  <h1>hello world</h1>
   <div class='ct'>
     <div class='box'>1</div>
     <div class='box'>2</div>
     <div class='box'>3</div>     
   </div>
```

![image.png](http://upload-images.jianshu.io/upload_images/9425951-196ef3500a1a07aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### 3.3.2 绝对定位时偏移量为百分比
当top,left,right,bottom的单位是百分比时，它相对的是它的父容器的宽高

###### 3.3.3 绝对定位元素在父容器中水平垂直居中
第一种方法是：用户父容器的宽高50%减去自身宽高的一半。这种写法的前提是自身必须设置了宽高。但是，绝对定位会使块级元素展现行内元素的特性，宽度由内容决定。这样的话，这种写法的适应性就不太好了。

```
.ct > div:nth-of-type(2){
  position:absolute;
  background-color:green;
  top: calc(50% - 25px);
  left:calc(50% - 50px);
}
```

第二种写法是使用`transform: translate(-50%,-50%)`  translate中的百分比相对的块级元素自身的宽高。 top,left,transform此时的含义是，在父容器50%宽高基础上再移动自身宽高的-50%
```
.ct > div:nth-of-type(2){
  position:absolute;
  background-color:green;
  width:auto; /*设置宽高由内容决定*/
  height: auto;
  top: 50%;
  left:50%;
  transform: translate(-50%,-50%);
}
```


##### 3.4 绝对定位，块级元素行内元素特性转换
绝对定位也会出现浮动定位块级元素和行内元素特性发生改变的特性。详见**浮动**章节中的`块级元素浮动宽度收缩`和`行内元素浮动后以块级元素特性呈现`小节
> 绝对定位宽度是收缩的，如果想撑满父容器，可以设置 width: 100%

``` html
<div style="position: absolute; background: yellow; margin:10px">hello</div>
<div style="position: absolute; background: red;width:100%;margin:50px 0px 0px 10px">hello</div>
```
![image.png](http://upload-images.jianshu.io/upload_images/9425951-7a631f7a0b0c7461.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
