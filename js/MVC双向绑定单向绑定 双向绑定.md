## 1. 遗留问题1: controller中重复的绘制页面代码
在[MVC双向绑定单向绑定 面向对象](https://github.com/monalisali/articles/blob/master/js/MVC%E5%8F%8C%E5%90%91%E7%BB%91%E5%AE%9A%E5%8D%95%E5%90%91%E7%BB%91%E5%AE%9A%20%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1.md) 一文中我们把model,view,controller这3个对象封装到了一个mvc.js文件中，以便不同的页面进行调用。

那么这样是否完美了呢？首先我们观察到，在构建controller对象时，每次操作完成后调用`this.view.render(this.model.data)()`来绘制页面

![image.png](https://upload-images.jianshu.io/upload_images/9425951-732a70477feb7039.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个缺陷可以使用事件机制，或者说“发布-订阅者”模式来进行优化

### 使用事件机制进行优化

#### 修改mvc.js文件

添加了一个EventHub class，它提供on,emit函数来注册和触发事件。
- on(eventName,fn): eventName：自定义的事件名称； fn：事件处理函数。
- emit(eventName,params): eventName: 自定义的事件名称; params:事件处理函数的参数。

通过on函数，我们可以定义一个事件名称，如:'changed'和事件处理函数如：绘制页面代码 `this.view.render(this.model.data)()` 然后，在需要绘制页面时只要使用emit函数触发'changed'事件即可，emit函数会自动去执行on函数注册的'changed'事件处理函数。

显然，这样的代码维护性、扩展性更好。

```
class EventHub {
  constructor(){
    //缓存事件处理函数
    this.events = {}
  }
  //注册自定义事件
  //eventName: 自定义事件名称
  //fn：事件处理函数
  on(eventName, fn){
    if(!this.events[eventName]){
      this.events[eventName] = []
    }
    this.events[eventName].push(fn)
  }
  //触发自定义事件
  //eventName： 自定义事件名称
  //parmas： 事件处理函数参数
  emit(eventName, params){
    let fnList = this.events[eventName]
    fnList.map((fn)=>{
      fn.apply(null, params)
    })
  }
  //删除事件
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

class Model extends EventHub{
  constructor(options) {
    super()
    this.data = options.data || {}
    this.resource = options.resource
    this.baseUrl = options.baseUrl || '/'
  }
  fetch(id) {
    return axios.get(this.baseUrl + this.resource + 's/' + id)
      .then(({data})=>{ this.data = data; this.emit('changed') })
  }
  create(data) {
    return axios.post(this.baseUrl + this.resource + 's', data)
      .then(({data})=>{ this.data = data; this.emit('changed') })
  }
  destroy() {
    let id = this.data.id
    return axios.delete(this.baseUrl + this.resource + 's/' + id)
      .then(()=>{ this.data = {}; this.emit('changed') })
  }
  update(newData) {
    let id = this.data.id
    return axios.put(this.baseUrl + this.resource + 's/' + id, newData)
      .then(({data})=>{ 
        this.data = data; 
        this.emit('changed')
      })
  }
}

class View {
  constructor({
    el, template
  }) {
    this.el = el
    this.$el = $(el)
    this.template = template
  }
  render(data) {
    let html = this.template
    for (let key in data) {
      let value = data[key]
      html = html.replace(`__${key}__`, value)
    }
    this.$el.html(html)
  }
}

class Controller {
  constructor({view, model, events, init, ...rest }) {
    this.view = view 
    this.model = model
    this.events = events
    //把其他方法都拷贝到this上来
    Object.assign(this, rest)
    this.bindEvents()
    this.view.render(this.model.data)
    //构建controller对象后，就会执行init函数。进行第一次页面绘制
    init.apply(this, arguments)
  }
  //使用了事件委托，事件都是绑定在根元素上的
  bindEvents() {
    this.events.map((e) => {
      this.view.$el.on(e.type, e.el, this[e.fn].bind(this))
    })
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

注意`var controller = new Controller`中的init函数。它首先注册了changed事件，并指明了changed事件的处理函数为`this.view.render(this.model.data)`。 其次`this.model.fetch(1);`再也不用写`this.view.render(this.model.data)`了。 

```
init(options) {
    this.model.on('changed', ()=>{
      this.view.render(this.model.data)
    })
    
    this.model.fetch(1);
  },
```

因为`this.model.fetch(1)`会触发change事件(`this.emit('changed')`)，从而执行changed事件的处理函数。而这个处理函数在注册changed事件时就已经指定了。

同理，updateModel函数也去掉了`this.view.render(this.model.data)`
```
 updateModel(newData) {
    this.model.update(newData)
  }
```

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
  
  //updateModel(newData) {
  //  this.model.update(newData).then(()=>{
  //    this.view.render(this.model.data)
  //  })
  //},
  
  updateModel(newData) {
    this.model.update(newData)
  }
})
```


## 2. 遗留问题2: 页面更新数据的粒度太粗了

我们可以观察一下View class中的render函数，发现更新html的代码为`this.$el.html(html)` 显然，它会把整个html代码替换掉
```
  render(data) {
    let html = this.template
    for (let key in data) {
      let value = data[key]
      html = html.replace(`__${key}__`, value)
    }
    this.$el.html(html)
  }

```

首先，我们在构造View对象时，为template属性添加一个input元素后
```
<div>
  书名：《__name__》，
  数量：__number__
</div>
<div>
   <input />
</div>
<div class="actions">
  <button id="increaseByOne">加1</button>
  <button id="decreaseByOne">减1</button>
  <button id="square">平方</button>
  <button id="cube">立方</button>
  <button id="reset">归零</button>
</div>
```
- 在input元素中输入100
- 点击任意按钮
- **input元素中输入的100没有了**

出现这个bug的原因就在于render函数替换了整个html内容，但是input中的数据并没有被View对象记录下来，替换html后自然就没有了。
那么如何解决这个问题呢？目前分为了两派：
1. 用户输入了什么，就记录在JS的data里(数据绑定的初步思想)
2. 不要简单粗暴的操作innerHtml,而是只更新需要更新的部位（虚拟DOM的初步思想）

Angular是基于第一个思想的，React是基于第二个思想的。

方案1的实现思路是： 在View class中增加一个data属性，用来记录input的数据。同时还要监听input的onchange事件以获取数据。然后在render页面的时候用data属性记录的值来回填input元素。

方案2的实现思路是：只修改数据修改了的元素，其他地方不动。

## 3. 遗留问题3: 元素的事件是用JS绑定的
按钮的时间是在构造Controller对象时，用JS绑定的。那是否可以直接在html中直接进行绑定呢？这样显然方便很多。
虽然这种方式违反了正交元素，但是毕竟这种方式太方便了，以至于几乎所有的框架都提供这种功能。




