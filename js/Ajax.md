## callback的使用
**注意1中的写法。**
对于本例来说，也可以像2这么写。但是2不好的地方在于不能明确的知道传递的参数renderNews是个变量还是一个方法。而且如果要在调用renderNews前需要再对数据进行一些处理的话，就要修改xhr.onload或者renderNews了。如果getNews()需要在多个地方用到，而且这种数据处理有不尽相同的话，就比较麻烦了。

所以最好还是用1的写法，比较灵活。定义一个匿名函数作为getNews的callback参数，同时这个匿名函数是带形参的。这样的话，在调用xhr.onload中的callback(newsJson)时就会把newsJson作为实参传递给这个匿名函数了。然后，我们可以在这个匿名函数中对newsJson做进一步处理了。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>html</title>
  <style>
    .container {
      width: 800px;
      margin: 0 auto;
    }

    .news {
      margin: 0;
      padding: 0;
      list-style: none;
    }

    .news >li {
      border: 1px solid #ccc;
      padding: 5px 10px;
      margin: 10px;
      color: #666;

    }
  </style>
</head>
<body>
  <div class="container">
    <ul class="news">
      <!-- <li>我是新闻</li> -->
    </ul>
    <button id="change">换一组</button>
  </div>

  <script>
    var newsContainer = document.querySelector('.news')
    var changeBtn = document.querySelector('#change')
    
    //1. 推荐
    getNews(function(news){
      renderNews(news)
    })
  
    //2. 可用，但不灵活
   //getNews(renderNews)
    
    changeBtn.onclick = function() {
      getNews(function(news){
        renderNews(news)
      })
    }


    function getNews(callback) {
      var xhr = new XMLHttpRequest()
      xhr.open('GET', '/getNews', true)
      xhr.onload = function() {
        var newsJson = JSON.parse(xhr.responseText)
        callback(newsJson)
      }
      xhr.send()     
    }

    function renderNews(news) {
      newsContainer.innerHTML = ''
      var fragement = document.createDocumentFragment()
      news.forEach(function(newsStr) {
        var node = document.createElement('li')
        node.innerText = newsStr
        fragement.appendChild(node)
      })
      newsContainer.appendChild(fragement)
    }
  </script>
</body>
</html>
```

## 自己封装一个Ajax

```
function ajax(opts){
    var url = opts.url
    var type = opts.type || 'GET'
    var dataType = opts.dataType || 'json'
    var onsuccess = opts.onsuccess || function(){}
    var onerror = opts.onerror || function(){}
    var data = opts.data || {}

    var dataStr = []
    for(var key in data){
        dataStr.push(key + '=' + data[key])
    }
    dataStr = dataStr.join('&')

    if(type === 'GET'){
        url += '?' + dataStr
    }

    var xhr = new XMLHttpRequest()
    xhr.open(type, url, true)
    xhr.onload = function(){
        if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
            //成功了
            if(dataType === 'json'){
                onsuccess( JSON.parse(xhr.responseText))
            }else{
                onsuccess( xhr.responseText)
            }
        } else {
            onerror()
        }
    }
    xhr.onerror = onerror
    if(type === 'POST'){
        xhr.send(dataStr)
    }else{
        xhr.send()
    }
}

ajax({
    url: 'http://api.jirengu.com/weather.php',
    data: {
        city: '北京'
    },
    onsuccess: function(ret){
        console.log(ret)
    },
    onerror: function(){
        console.log('服务器异常')
    }
})
```

[参考](http://book.jirengu.com/fe/%E5%89%8D%E7%AB%AF%E5%9F%BA%E7%A1%80/Javascript/ajax.html)
