## 什么是bind()
`call()` 和 `apply()`都是直接**调用**函数。`bind()`并**不调用函数**，而是返回一个新的函数。这个新的的函数被调用时会call原来的函数，call的参数由你决定。

## 举例说明

下面这种写法很常见，element属性指定元素，onClick属性指定点击操作的处理函数，bindEvent属性绑定元素和事件。
特别注意bindEvent属性中的写法，我们的需求是：在bindEvent属性中为#div1元素绑定事件处理函数view.onClick 

常规写法：
```
var view = {
  element:$('#div1'),
  bindEvent: function(){
     //bindOnclick应该直接使用匿名函数，这里只是为了解释时方便
     this.element.onclick = function bindOnClick(){
        //不能写成this.onClick()
        //等价于view.onClick.call(view)
        view.onClick();
     }
  }，
  onClick:function(){
    this.element.addClass('active');
  }
}

```

如果这样写： `this.element.onclick = this.onclick`的话是不行的。因为'#div1'被点击时`this.onclick`的this指的是'#div1'元素。所以我们不得不用一个函数(bindOnClick)把我们正常想写的代码给包起来。

那有没有更好的写法呢？

首先考虑，是否可以使用call()来解决这个，如: `this.element.onclick = this.onClick.call(this)` 
可惜的是，不行。  bindOnClick函数并不能使用`this.onClick.call(this)`来代替。 原因有两点：
1. 当'#div1'被点击时，`this.onClick.call(this)`中的this并不是view，而是'#div1'
2. `this.onClick.call(this)` 会立即执行，而不是在'#div1'元素被点击时执行。

那么假设已有函数f，是不是有一种方法可以为f生成一个新函数，新函数在执行时会调用f。而不用再创建一个匿名函数来包裹f了。
这种场景就是bind()的用武之地了： 
``` 
this.element.onclick = this.onClick.bind(this) //bind()传递的this就是view
```
**这句代码等价于上方`function bindOnClick`中的整个代码，** 而且bind()还能指定参数(this,agruments)。

使用bind()是不是简便和优雅很多呢？


## bind的本质
下面的代码揭示了bind的本质：bind方法其实就是生成了 '确定了this和args的f'这个函数而已。
```
function f (){
   console.log(this);
   console.log(arguments);
}

//这就是bind方法生成的函数
function 确定了this和args的f (){
    f.call({name:'tom'},1)
}

var f1 = f.bind({name:'tom'},1);
f1();
确定了this和args的f();


```


