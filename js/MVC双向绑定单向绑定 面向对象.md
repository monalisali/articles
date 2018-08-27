
## 遗留的问题
在[MVC双向绑定单向绑定 MVC](https://github.com/monalisali/articles/blob/master/js/MVC%E5%8F%8C%E5%90%91%E7%BB%91%E5%AE%9A%E5%8D%95%E5%90%91%E7%BB%91%E5%AE%9A%20%20MVC.md) 一文中，我们已经通过model,view和controller这3个对象把代码优化了一下。使得代码降低了耦合度，摆脱了意大利面条式的代码。

可是再深入思考一下的话，发现还有优化的空间。因为model,view,controller这3个对象是封装在当前页面的，如果换一个页面的话还要把这3个对象再写一遍。更好的方式是把model,view,controller这3个对象的公共部分给封装起来。然后，各个页面根据自己的需要构建特定的model，view，controller对象。


## 实现
建立一个名为mvc.js的文件，文件中包含Model,View,Controller这3个class（构造函数）

```
class Model{
  constructor(options) {
    this.data = options.data || {}
    this.resource = options.resource
    this.baseUrl = options.baseUrl || '/'
  }
  fetch(id) {
    return axios.get(this.baseUrl + this.resource + 's/' + id)
      .then(({data})=>{ this.data = data })
  }
  create(data) {
    return axios.post(this.baseUrl + this.resource + 's', data)
      .then(({data})=>{ this.data = data })
  }
  destroy() {
    let id = this.data.id
    return axios.delete(this.baseUrl + this.resource + 's/' + id)
      .then(()=>{ this.data = {} })
  }
  update(newData) {
    let id = this.data.id
    return axios.put(this.baseUrl + this.resource + 's/' + id, newData)
      .then(({data})=>{ 
        this.data = data; 
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

## 使用

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
  init(options) {
    
    this.model.fetch(1)
      .then(() => {
        this.view.render(this.model.data)
      })
  },
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
    this.model.update(newData).then(()=>{
      this.view.render(this.model.data)
    })
  }
})


```


