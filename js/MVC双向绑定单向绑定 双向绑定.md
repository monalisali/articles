## 遗留问题
在[MVC双向绑定单向绑定 面向对象](https://github.com/monalisali/articles/blob/master/js/MVC%E5%8F%8C%E5%90%91%E7%BB%91%E5%AE%9A%E5%8D%95%E5%90%91%E7%BB%91%E5%AE%9A%20%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1.md) 一文中我们把model,view,controller这3个对象封装到了一个mvc.js文件中，以便不同的页面进行调用。

那么这样是否完美了呢？首先我们观察到，在构建controller对象时，每次操作完成后调用`this.view.render(this.model.data)()`来绘制页面

![image.png](https://upload-images.jianshu.io/upload_images/9425951-732a70477feb7039.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个缺陷可以使用事件机制，或者说“发布-订阅者”模式来进行优化

## 使用事件机制进行优化

### 修改mvc.js文件

```
class EventHub {
  constructor(){
    //events对象用来缓存所有事件处理函数
    this.events = {}
  }
  //订阅
  //eventName: 自定义的事件名称
  //fn:自定义的事件处理函数
  on(eventName, fn){
    if(!this.events[eventName]){
      this.events[eventName] = []
    }
    this.events[eventName].push(fn)
  }
  //发布（触发）
  //eventName:时间名称
  //params:事件处理函数的参数
  emit(eventName, params){
    let fnList = this.events[eventName]
    fnList.map((fn)=>{
      fn.apply(null, params)
    })
  }
  //删除事件的订阅
  off(eventName, fn){
    let fnList = this.events[eventName]
    for(let i =0; i<fnList.length; i++){
      if(fnList[i] === fn){
        delete fnList[i]
        break
      }
    }
  }
}
```

### 使用
#### HTML

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://cdn.bootcss.com/axios/0.17.1/axios.min.js"></script>
  <title>JS Bin</title>
</head>
<body>
  <div id="app">
    
  </div>

  <div id="status"></div>

</body>
</html>
```

#### JS

```
axios.interceptors.response.use(function (response) {
  let {config: {url, method, data}} = response
  data = JSON.parse(data||'{}')
  let row = {
    id: 1, name: 'JavaScript高级程序设计', number: 2
  }
  if(url === '/books/1' && method === 'get'){
    response.data = row
  }else if(url === '/books/1' && method === 'put'){
    response.data = Object.assign(row, data)
  }
  return response
})



var model = new Model({
  resource: 'book',
  data: {
    id: null,
    number: 0,
    name: null
  }
})

var view = new View({
  el: '#app',
  template: `
<div>
  书名：《__name__》，
  数量：__number__
</div>
<div class="actions">
  <button id="increaseByOne">加1</button>
  <button id="decreaseByOne">减1</button>
  <button id="square">平方</button>
  <button id="cube">立方</button>
  <button id="reset">归零</button>
</div>
  `,
})


var controller = new Controller({
  view: view,
  model: model,
  events: [
    { type: 'click', el: '#increaseByOne', fn: 'add' }, 
    { type: 'click', el: '#decreaseByOne', fn: 'minus' },
    { type: 'click', el: '#square', fn: 'square' },
    { type: 'click', el: '#cube', fn: 'cube' }, 
    { type: 'click', el: '#reset', fn: 'reset' }
  ],
  
//   init(options) {   
//     this.model.fetch(1)
//       .then(() => {
//         this.view.render(this.model.data)
//       })
//   },
  
 init(options) {
    this.model.on('changed', ()=>{
      this.view.render(this.model.data)
    })
    
    this.model.fetch(1);
  },
  
  //下面的方法都是其他方法，就是构造函数中的...rest指代的内容
  add() {
    let newData = {number: this.model.data.number + 1}
    this.updateModel(newData)

  },
  minus() {
    // 注意这里有 bug
    this.model.data.number = this.model.data.number - 1
    this.updateModel(this.model.data)
  },
  square() {
    let newData = {number: Math.pow(this.model.data.number, 2)}
    this.updateModel(newData)
  },
  cube() {
    let newData = {number: Math.pow(this.model.data.number, 3)}
    this.updateModel(newData)
  },
  reset() {
    this.updateModel({number: 0})
  },
  updateModel(newData) {
    this.model.update(newData)
  }
})
```



