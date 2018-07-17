
## 什么是callback
callback有两种理解方式，一个是把callback以名词理解，另一个是以动词理解
- 名词：作为参数的函数就是callback
- 动词: 作为参数的函数被调用时的那个动作叫做callback

```
array.sort(function(a,b){return a-b;})
```
传递给sort()的匿名函数就是一个callback，这个匿名函数在sort()中被调用时的操作也叫做callback


**回调和异步没有什么特殊关系**，只是异步中经常用到回调而已。 

```
setTimeout(function(){ },300)
```
setTimeout()是异步的，它用到了回调（一个匿名函数），但这个匿名函数和setTimeout()并没有什么特别的关系。
