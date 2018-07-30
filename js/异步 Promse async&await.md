
## 什么是Promise

Promise 是一个对象，对象里存储一个状态，这个状态是可以随着内部的执行转化的，为以下三种状态之一：等待态(Pending)、完成态(Fulfilled)、拒绝态(Rejected)。

一开始，我们先设置好等状态从 pending 变成 fulfilled 和 rejected 的预案（当成功后我们做什么，失败时我们做什么）。

Promise 启动之后，当满足成功的条件时我们让状态从 pending 变成 fullfilled （执行 resolve）；当满足失败的条件，我们让状态从 pending 变成 rejected（执行 reject）

## Promise的then方法
Promsise对象的then方法接受两个参数，这个两个参数都是callback，一个表示成功，另一个表示失败。

axios也是遵守Promise规范的，所以它也有then()

```
axios({
   url: XXXXX
}).then(s1,e1)
  .then(s2,e2)
  .then(s3,e3)
  
```
3个then()的执行逻辑是: s1或者e1执行成功，只要不报错就执行s2,否则执行e2; s2或者e2执行成功，只要不报错就执行s3,否则执行e3

## Promise 举例

```
function getIp() {
  var promise = new Promise(function(resolve, reject){
    var xhr = new XMLHttpRequest()
    xhr.open('GET', 'xxxx/getIp', true)
    xhr.onload = function(){
      var retJson = JSON.parse(xhr.responseText) 
      resolve(retJson.ip)
    }
    xhr.onerror = function(){
      reject('获取IP失败')
    }
    xhr.send()
  })
  return promise
}

function getCityFromIp(ip) {
  var promise = new Promise(function(resolve, reject){
    var xhr = new XMLHttpRequest()
    xhr.open('GET', 'xxxx/getCityFromIp?ip='+ip, true)
    xhr.onload = function(){
      var retJson = JSON.parse(xhr.responseText)
      resolve(retJson.city)
    }
    xhr.onerror = function(){
      reject('获取city失败')
    }
    xhr.send()
  })
  return promise
}
function getWeatherFromCity(city) {
  var promise = new Promise(function(resolve, reject){
    var xhr = new XMLHttpRequest()
    xhr.open('GET', 'xxxx/getWeatherFromCity?city='+city, true)
    xhr.onload = function(){
      var retJson = JSON.parse(xhr.responseText)
      resolve(retJson)
    }
    xhr.onerror = function(){
      reject('获取天气失败')
    }
    xhr.send()
  })
  return promise
}

getIp().then(function(ip){
  return getCityFromIp(ip)
}).then(function(city){
  return getWeatherFromCity(city)
}).then(function(data){
  console.log(data)
}).catch(function(e){
  console.log('出现了错误', e)
})

```

