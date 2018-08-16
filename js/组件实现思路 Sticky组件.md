## 在页面中实现Sticky效果

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


