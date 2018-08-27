## 遗留问题
在[MVC双向绑定单向绑定 面向对象](https://github.com/monalisali/articles/blob/master/js/MVC%E5%8F%8C%E5%90%91%E7%BB%91%E5%AE%9A%E5%8D%95%E5%90%91%E7%BB%91%E5%AE%9A%20%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1.md) 一文中我们把model,view,controller这3个对象封装到了一个mvc.js文件中，以便不同的页面进行调用。

那么这样是否完美了呢？首先我们观察到，在构建controller对象时，每次操作完成后调用调用`upodateModel()`来绘制页面

![image.png](https://upload-images.jianshu.io/upload_images/9425951-7bf268c25fdc3b06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个缺陷可以使用事件机制，或者说“发布-订阅者”模式来进行优化

## 使用事件机制进行优化
