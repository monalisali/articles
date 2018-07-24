## 什么是bind()
`call()` 和 `apply()`都是直接**调用**函数。而`bind()`并**不调用函数**，而是返回一个新的函数。这个新的的函数被调用时会call原来的函数，call的参数由你决定。

## 举例说明

下面这种写法很常见，element属性指定元素，onClick属性指定点击操作的处理函数，bindEvent属性绑定元素和事件。
特别注意bindEvent属性中的写法，我们的需求是：在bindEvent属性中为#div1元素绑定事件处理函数view.onClick 

*整个`function bindOnClick`的代码可以直接使用`this.onclick.bind(this)`来代替。这里只是用`function bindOnClick`来演示bind和call之间的关系*

```
var view = {
  element:$('#div1'),
  bindEvent: function(){
     //
     this.element.onclick = function bindOnClick(){
        //this：这里的this已经变了，不再是view对象。 这个bindOnClick函数中的this为#div1,在用户点击#div1元素时由浏览器指定
        //所以不能写成this.onClick()
        view.onClick() => view.onClick.call(view);
     }
  }，
  onClick:function(){
    this.element.addClass('active');
  }
}

```

首先考虑，可以使用call()来解决这个问题吗？如: `this.element.onclick = this.onClick.call(this)` 
可惜的是， `bindOnClick()`并不能使用`this.onClick.call(this)`来代替。 因为使用call的话会立即执行onClick(),而我们并不希望这样。 
所以，这种场景就是bind()的用武之地了： ` this.element.onclick = this.onClick.bind(this)` **这句代码等价于上方`function bindOnClick`中的整个代码， **而且bind()还能指定参数,此时bind()传递的this就是view

使用bind()是不是简便和优雅很多呢？
