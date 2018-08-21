
## 设计接口
可以设想用户会这样使用Suggestion组件

- input: 需要初始化的元素
- search(text,callback)
  text: 输入的数据
  callback: 回调函数。获取数据一般都是需要从server端拿的，获取完成后用这个callback来绘制组件的下拉框
- emptyTemplate： 返回值为空时显示内容
- loadingTempate：数据在加载时显示的内容
```
var suggestion = new Suggestion({
   input: '#x',
   search: function(text,callback){
      callback(['a','aa','bb']);
   },
   emptyTemplate: '没有结果',
   loadingTempate: '<b>正在加载中.....</bar>'

})
```
