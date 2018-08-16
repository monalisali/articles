## Tab 组件
1. 使用组件的约定
- 容器div必须包含样式tabs
- tab的bar和内容都是ol,li结构
- tab bar的ol必须包含样式tabs-bar
- tab content的ol必须包含样式tabs-content


2.组件代码

2.1 JS
Tab组件的JS代码有几个技巧需要关注一下：
1. 在构造函数Tabs中，把选择器selector选中的元素赋值给了elements属性。这样可以方便后续函数使用selector元素，如：init函数
2. 


```
function Tabs(selector){
  this.elements = $(selector);
  this.init();
  this.bindEvents();
};

//init:默认选中第一个tab和content
Tabs.prototype.init = function(){
   this.elements.each(function(index,element){
      $(element).children('.tabs-bar').children('li').eq(0).addClass('active');
      $(element).children('.tabs-content').children('li').eq(0).addClass('active');
   });
};

Tabs.prototype.bindEvents = function(){
   $('.tabs').on('click','.tabs-bar > li',function(e){
     var $li = $(e.currentTarget);
     //设置点击的li为active
     $li.addClass('active').siblings().removeClass('active');
     //显示对应的content
     var index = $li.index();
     var $content = $li.closest('.tabs').find('.tabs-content > li').eq(index);
     $content.addClass('active').siblings().removeClass('active');
  }); 
}

```

2.2 CSS
```
.tabs{}
.tabs > ol {
  list-style:none;
  margin:0;
  padding:0;
}
.tabs > ol.tabs-bar{
  display:flex;
  border-bottom:1px solid red;
}
.tabs > ol.tabs-bar > li{
  padding: 4px 8px;
  border:1px solid transparent;
  border-bottom:0;
}
.tabs > ol.tabs-bar > li:hover{
  border-color:red;
}
.tabs > ol.tabs-bar > li.active{
  border-color:blue;
}
.tabs > ol.tabs-content > li{
  display:none;
}

.tabs > ol.tabs-content > li.active{
  display:block
}
```

## 使用示例
使用这个Tab组件时，HTML只要根据组件的要求包含必要的元素和class即可。而JS的话，只需要执行一行JS

HTML
```
<div class="tabs xxx"> 
    <ol class="tabs-bar">
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ol>
    <ol class="tabs-content">
      <li>content1</li>
      <li>content2</li>
      <li>content3</li>
    </ol>  
  </div>
  
  
 <div class="tabs xxx"> 
    <ol class="tabs-bar">
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ol>
    <ol class="tabs-content">
      <li>content1</li>
      <li>content2</li>
      <li>content3</li>
    </ol>  
 </div>
```

JS
```
var tabs = new Tabs('.xxx');
```
