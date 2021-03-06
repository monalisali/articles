## 要学清楚this和arguments的话，就要使用call()

如果使用call()来调用函数的话，this和arguments的值就很清楚了。call()的第一参数就是this,剩下的所有参数都是arguments
```
function fn(){
  console.log(this);
  console.log(arguments)
}

fn.call(); //windows, []
fn.call({name: 'tom'}); // {name: 'tom'}, []
fn.call({name: 'tom'}, 1); // {name: 'tom'}, [1]
fn.call({name: 'tom'}, 1,2); // {name: 'tom'}, [1,2]

```

用`()`来调用函数的形式，如`fn()`其实是阉割版的`call()` 阉割的方面在于：**只能传递参数，而无法传递this，this的值是什么只能让浏览器自己猜了。**
而使用`call()`就没有这个问题了，传给this是什么就是什么。
```
//这个windows就是浏览器猜的
fn(); //windows, []
```

## 为什么this必须是对象
还是上例，现在这样调用`fn.call(10,1)` 显然10就是this。但是打印出来后，发现this为Number对象。那为什么传递的明明是基础类型的10，而this却变成了Number对象呢？

先假设没有this的情况，有如下代码

```
var person = {
   name: 'tom',
   sayName: function(person){
      console.log('Hi,my name is: ' + person.name);
   }

}

person.sayName(person)

```
`sayName()`要能获取到person对象的name属性，它就必须接受一个person对象。 代码的确可以正常工作，但是非常的奇怪：person调用自己的`sayName()`，但`sayName()`却不知道person对象是什么，必须在调用时传递给`sayName()`才可以。

如果能这样写代码就好了 `person.sayName()`  这样的话就非常的直观：person对象调用自己的`sayName()`, `sayName()`认得person对象，在方法体内可以直接使用person对象。

所以JS之父就发明了this这个语法糖。 this的作用其实**就是个占位符**，传给它什么值它就是什么值。 **括号调用时，点号之前的内容就是this的值**

上面的代码就可以改写成
```
var person = {
   name: 'tom',
   sayName: function(person){
      console.log('Hi,my name is: ' + this.name);
   }

}

person.sayName() //点号之前的person对象就是this

```
> 所以说，this就是对象与函数之间的羁绊。它把对象和函数关联起来了，就像关联person和sayName()

如果函数之前没有对象去调用它呢？那么此时的this代表什么？
```
var fn2 = person.sayName;
fn2() // 此时的this是window  打印的结果是： Hi, my name is
window.name = 'xxxx'
fn2();// Hi,my name is xxxx

```
如果你不想吃这个语法糖的话，可以使用call()来显示的指定this
```
person.sayName.call(person)
```
你甚至可以传递任何对象给this
```
person.sayName.call({name: 'yy'}) Hi,my name is yy
```

那么在回到开头的问题：为什么使用call()传递的this参数是基本类型10，但是fn()打印出来的却是个Number对象。这是JS在设计之初的一个缺陷，JS会对传递进来的this参数擅自进行修改。如果不想让JS瞎操作的话，可以在fn()中使用 `'use strict'`

```
function fn(){
  'use strict'
  console.log(this);
  console.log(arguments)
}

```



## call() 和 apply()

call()和apply()几乎一样，区别就在于apply接受的参数是一个数组
```
var a = [1,2,3,4];
sum.call(undefined,a[0],a[1],a[2],a[3]);
sum.apply(undefined,a);

```



