## 在页面中实现Sticky效果 (非封装)

#### html
div topbar必须用一个div包括起来。否则向下拉动时，mian会**一下子**消失60px(topbar的高度)，没有一个渐进的过程，显得很突兀。这个div topbarPlaceholder可以看做一个占位符，它是topbar实现sticky效果的关键。

```
<div class="topbarPlaceholder">
    <div id="topbar">
      topbar
    </div>
  </div>
    
  <main>
    主要内容
     <p>段落1</p>
     <p>段落2</p>
     <p>段落3</p>
     <p>段落4</p>
     <p>段落5</p>
     <p>段落6</p>
     <p>段落7</p>
     <p>段落8</p>
     <p>段落9</p>
     <p>段落10</p>
     <p>段落11</p>
    <button>粘着的button</button>
     <p>段落12</p>
  </main>

```

#### css

```
*{margin:0;padding:0}

#topbar{
  background-color:green;
  height:60px;
  color:white;
  text-align:center;
}

#topbar.sticky{
  position:fixed;
  top:0;
  left:0;
  width:100%;
  opacity:0.5;
}

.topbarPlaceholder{
  height:60px;
}

main{
  background:#ddd;
  height:1800px;
}

button.sticky{
  position:fixed;
  left:0;
  top:60px
}

```

#### JS

```
var buttonOffset = $('button').offset();
$(window).on('scroll',function(){
  //把topbar黏住
  var scrollY = window.scrollY;
  if(scrollY > 0){
    $('#topbar').addClass('sticky')
  }else{
     $('#topbar').removeClass('sticky')
  }
  
  
  //把按钮黏在topbar的下方
  if(scrollY + 60 > buttonOffset.top){
    $('button').addClass('sticky')
  }else{
    $('button').removeClass('sticky')
  }
})

```

## Sticky组件封装

### 注意点
1. 每个需要sticky效果的元素外面都要添加一个**占位div**，以防sticky元素变成绝对定位后页面的结构发生变化而挂掉。

### 使用约定
1. `new Sticky([selector],<n>)` 把需要Sticky效果的元素和高度传递给构造函数，选择器是必须的，高度是可选的。


### 代码

#### CSS

```
*{margin:0;padding:0}

#topbar{
  background-color:green;
  height:60px;
  color:white;
  text-align:center;
}

#topbar.sticky{
  position:fixed;
  top:0;
  left:0;
  width:100%;
  opacity:0.5;
}

.topbarPlaceholder{
  height:60px;
}

main{
  background:#ddd;
  height:1800px;
}

button.sticky{
  position:fixed;
  left:0;
  top:60px
}
```

#### JS
```
//selector: 需要sticky效果的元素
//n: 元素的高度
function Sticky(selector,n){
    this.elements = $(selector);
    this.offset = n || 0;
    this.addPlaceholder();
    this.cacheOffsets();
    this.listenToScroll();
}

//为每一个需要sticky的元素都添加一个wrapper
//否则，一旦sticky的元素变成绝对定位后，页面可能就会挂掉
Sticky.prototype.addPlaceholder = function(){
  this.elements.each(function(index,element){
    $(element).wrap('<div class="stickyPlaceholder"></div>');
    $(element).parent().height($(element).height());
  })
}

Sticky.prototype.cacheOffsets = function(){
    this.offsets = [];
    var sticky = this;
    this.elements.each(function(index,element){
      sticky.offsets[index] = $(element).offset()
    })
  
}

Sticky.prototype.listenToScroll = function(){
   var sticky = this; 
   $(window).on('scroll',function(){
      var scrollY = window.scrollY;
      sticky.elements.each(function(index,element){
        var $element = $(element);
        if(scrollY + sticky.offset > sticky.offsets[index].top){
          $element.addClass('sticky');          
        }else{
          $element.removeClass('sticky')
        }
        
      })
    })

}

```

## 组件使用

#### html

```
<!DOCTYPE html>
<html>
<head>
<meta name="description" content="Sticky 组件">
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
  <script src="https://code.jquery.com/jquery-3.1.0.js"></script>
</head>
<body>
    <div id="topbar">
      topbar
    </div>

  <main>
    主要内容
     <p>段落1</p>
     <p>段落2</p>
     <p>段落3</p>
     <p>段落4</p>
     <p>段落5</p>
     <p>段落6</p>
     <p>段落7</p>
     <p>段落8</p>
     <p>段落9</p>
     <p>段落10</p>
     <p>段落11</p>
    <button>粘着的button</button>
     <p>段落121211212</p>
  </main>
  
  
</body>
</html>
```


#### JS
```
new Sticky('#topbar')
new Sticky('button',60)

```

#### 效果演示

用元素检查可以发现：Sticky元素topbar和button的外面都裹了wrap div `<div class="stickyPlaceholder"></div>`。如果没有这个wrap的话，当Sticky元素变成绝对定位后，页面很有可能会挂掉。

下方截图是Sticky的效果，可以发现"段落11"和"段落12121212"在button变成绝对定位后，它们之间依然有一个空间（红色部分）。这个空间就是在button元素外面添加的**占位div**`<div class="stickyPlaceholder"></div>`

> ![image.png](https://upload-images.jianshu.io/upload_images/9425951-76a064c9538069af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)















