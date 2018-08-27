## 需求
页面加载时从db中获取最新的书籍信息。 页面提供一些按钮可以对书籍的数量进行编辑，如：加1，减1，平方等等。

## 没有MVC概念时的实现
需求十分的简单，我们直接可以实现它

#### HTML
```
 <div id="app"></div>
 <div id="status"></div>
```

#### JS

*为了简单起见，我们直接使用axios来模拟后端的返回内容*

```
axios.interceptors.response.use(function (response) {
  let {config: {url, method, data}} = response
  data = JSON.parse(data||'{}')
  let row = {
    id: 1, name: 'JavaScript高级程序设计', number: 2
  }
  
  if(url === '/books/1' && method === 'get'){ //获取数据
    response.data = row
  }else if(url === '/books/1' && method === 'put'){ //更新数据
    response.data = Object.assign(row, data)
  }
  return response
})


function fetchDb() {
  return axios.get('/books/1')
}

function saveDb(newData) {
  return axios.put('/books/1', newData)
}


//定义页面元素
var template = `
<div>
  书名：《__name__》，
  数量：<span id="number">__number__</span>
</div>
<div class="actions">
  <button id="increaseByOne">加1</button>
  <button id="decreaseByOne">减1</button>
  <button id="square">平方</button>
  <button id="cube">立方</button>
  <button id="reset">归零</button>
</div>
`



fetchDb().then((response) => {
  let result = response.data
  $('#app').html(
    template.replace('__number__', result.number)
      .replace('__name__', result.name)
  )

  //加1
  $('#increaseByOne').on('click', (e) => {
    let oldResult = parseInt($('#number').text(), 10)
    let newResult = oldResult + 1
    saveDb({number: newResult}).then(function({data}) {
      console.log(data)
      $('#number').text(data.number)
    })
  })

  //减1
  $('#decreaseByOne').on('click', (e) => {
    let oldResult = parseInt($('#number').text(), 10)
    let newResult = oldResult - 1
    saveDb({number: newResult}).then(({data}) => {
      $('#number').text(data.number)
    })
  })

  //平方
  $('#square').on('click', (e) => {
    let oldResult = parseInt($('#number').text(), 10)
    let newResult = Math.pow(oldResult, 2)
    saveDb({number: newResult}).then(({data}) => {
      $('#number').text(data.number)
    })
  })

  //立方
  $('#cube').on('click', (e) => {
    let oldResult = parseInt($('#number').text(), 10)
    let newResult = Math.pow(oldResult, 3)
    saveDb({number: newResult}).then(({data}) => {
      $('#number').text(data.number)
    })
  })

  //归零
  $('#reset').on('click', (e) => {
    let newResult = 0
    saveDb({number: newResult}).then(({data}) => {
      $('#number').text(data.number)
    })
  })
})

```

代码也十分的简单，我们仅仅定义了一个html template，绑定按钮事件。然后和后台交互就可以了。
这种实现的主要问题在于
- 每个操作与后台交互完成后，都要更新html template 
- 事件绑定函数的代码重复度太高了
- 业务逻辑和html的展示耦合度太高了

这种实现方式就是我们俗称的 **意大利面条式的代码**，虽然可以工作但不是很好。

## MVC
为了解决这些问题，有些程序员就发明了MVC这个概念。JS中的MVC也是从其他语言中借鉴过来的。
- M(Model): JS的数据操作
- V(View): HTML展示层
- C(Controller): 逻辑层。controller其实就是个垃圾堆，M和V之外的东西都可以算作是Controller


上例的代码可以使用MVC的概念进行优化

```
axios.interceptors.response.use(function(response) {
  console.log(response)
  let {
    config: {
      url, method, data
    }
  } = response
  data = JSON.parse(data || '{}')
  let row = {
    id: 1,
    name: 'JavaScript高级程序设计',
    number: 2
  }
  if (url === '/books/1' && method === 'get') {
    response.data = row
  } else if (url === '/books/1' && method === 'put') {
    response.data = Object.assign(row, data)
  }
  return response
})

let model = {
  data: {
    number: 0,
    name: ''
  },
  fetch(id) {
    return axios.get(`/books/${id}`).then((response)=>{
      this.data = response.data
    })
  },
  update(newData) {
    let id = this.data.id
    return axios.put(`/books/${id}`, newData).then(({data})=>{
      this.data = data
    })
  }
}

let view = {
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
  render(data) {
    let html = this.template.replace('__name__', data.name)
      .replace('__number__', data.number)
    console.log(data)
    $(this.el).html(html)
  }
}


var controller = {
  init({ view, model}) {
    this.view = view
    this.model = model
    this.view.render(this.model.data)
    this.bindEvents()
    console.log(1)
    this.fetchModel()
    console.log(2)
  },
  events: [
    { type: 'click', selector: '#increaseByOne', fnName: 'add' },
    { type: 'click', selector: '#decreaseByOne', fnName: 'minus' },
    { type: 'click', selector: '#square', fnName: 'square' },
    { type: 'click', selector: '#cube', fnName: 'cube' },
  ],
  bindEvents() {
    this.events.map((event)=>{
      $(this.view.el).on(event.type, event.selector, this[event.fnName].bind(this))
    })
  },
  add(){
    let newData = {number: this.model.data.number + 1}
    this.updateModel(newData)
  },
  minus(){
    let newData = {number: this.model.data.number - 1}
    this.updateModel(newData)
  },
  square(){
    let newData = {number: Math.pow(this.model.data.number, 2)}
    this.updateModel(newData)
  },
  cube(){
    let newData = {number: Math.pow(this.model.data.number, 3)}
    this.updateModel(newData)
  },
  fetchModel(){
    this.model.fetch(1).then(() => {
      this.view.render(this.model.data)
    })
  },
  updateModel(newData){
    this.model.update(newData).then(()=>{
      this.view.render(this.model.data)
    })
  }
}

controller.init({view, model})

```

优化后的代码有3个对象：model,view,controller 
- view: 接受数据，然后通过数据来绘制html；
- model: 与后台交互获取数据、更新数据
- controller：各种业务逻辑。如按钮的事件绑定等

3个对象各自独立，只在需要的地方进行联系。这样就使得代码的耦合度大大降低了。同时，需要主要`bindEvents`函数，利用它也大大降低了事件绑定的重复代码。
