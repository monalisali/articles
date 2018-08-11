##  1. 函数是什么
函数就是由可执行代码组成的**对象**

### 1.1 怎么理解函数是对象

```
var obj = {
   name: "tom",
   age:10,
   functionBody:'console.log("hello world")'
   
}

//定义一个call方法来执行x的functionBody
var objCommon = {
     myCall: function(x){
        eval(x.functionBody); // 用eval函数来执行字符串:console.log("hello world")
     }
}

obj.__proto__ = objCommon; // 生产环境千万不要这样写，这里只是为了说明问题
obj.myCall(obj);

```
从上面代码可以知道，所谓函数就是一段字符串: `console.log("hello world")`，这个字符串就是我们平时所说的函数体。 在需要执行的时候就去eval这个字符串。
我们知道，JS中的字符串都是由String这个函数对象创建的。那么既然string是对象，那么由string组成的**函数**当然也就是一个对象了。


## 2. 对象和函数的关系
### 2.1 函数是独立于对象的
**在JS中，函数不是对象的附庸。函数永远独立的保存在一块内存中**
```
var user = {
  name: 'jack',
  sayName: function(){
      console.log('say name');
   }
}
```
从下面这个内存图可以知道， 方法是独立的保存在一块内存中的。 user对象的sayName属性**只是保存了一个内存地址**，这个地址指向的内存才是方法真正存放的位置。

>  ![image.png](https://upload-images.jianshu.io/upload_images/9425951-a5147e9e84a3e0ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.2 对象与函数互相独立的坏处
```
var person = {
   name: 'tom',
   sayName: function(person){
      console.log('Hi,my name is: ' + person.name);
   }

}
person.sayName(person)
```
`sayName()`要能获取到person对象的name属性，它就必须接受一个person对象。 这个代码的确可以正常工作，但是非常的奇怪：person对象调用自己的`sayName()`，但`sayName()`却不知道person对象是什么，必须在调用时传递给`sayName()`才可以。

`sayName`不能获取到对象的原因是： JS中的函数很纯粹，纯粹到只接受参数，返回结果。

要是能这样写代码就好了 ：`person.sayName()` 。 这样的话就非常的直观：person对象调用自己的`sayName()`, `sayName()`认得person对象，在方法体内可以直接使用person对象。

### 2.3 连接对象与函数
要实现`sayName()`可以获取到person对象，需要两方面配合
1. 浏览器提供一个机制，一旦发现 `person.sayName()`这种调用方式，就把person对象自动的传递给`sayName()`方法
2. JS要能接受到这个传递过来的参数

可是这样就有一个悖论了：假设浏览器会自动把person对象传递给sayName(), 而且这种传递并不需要通过sayName()的参数。可是在调用sayName()时，又确实需要一个参数，不然是会报错的。
 
JS的解决方案是：既然浏览器传递person对象时不需要通过参数来传递，那么我索性就不提供参数了。那没有参数的话，函数怎么接受到person对象呢？ **为了解决这个问题： JS之父就发明了this， 先占个位置来代替对象，当浏览器（或其他任何东西）传值给这个this, 那么this的值就是什么**
 
所以，有了this后，可以把代码改成：
```
var person = {
   name: 'tom',
   sayName: function(){ // 这里不需要参数了
      console.log('Hi,my name is: ' + this.name);
   }
}
person.sayName()  
```
上面的`person.sayName() `是浏览器**隐式**的把person对象传递给`sayName()`的this。隐士传递this时，this就是方法前面的那个对象。
我们也可以使用call()来**显示**的传递this，用call的话传递任何对象都可以，是任由程序员控制的
```
person.sayName.call({name: 'mary'});//显示的 传递任何对象给sayName()的this
person.sayName.call(1) // 这个时候sayName中的this是1(非严格模式下会变成Number类型)，执行方法时会报错
```

#### 2.4. 练习题
```
var baba = {
   name: 'Mayun',
　　child: {          
         name: 'Sicong',
         sayName: function(){
             console.log(this.name)
         } 
   }
}

baba.child.sayName(); //Sicong;
baba.child.sayName.call(); // undefined
baba.child.sayName.call(undefined);//undefined;
baba.child.sayName.call({name:wangjianling}); // wangjianliang
```

`baba.child.sayName.call({name:wangjianling});` 把一个自定义的对象 `{name:wangjianling}`传递给了sayName(), 并且成功打印出了`wangjianling` 。

#### 2.5 this的尴尬地位
有没有发现用call时有个很奇怪的地方:  call的第一个参数是this，之后的参数是arguments 。 既然都是参数，为什么JS要对this进行特殊处理，而不是把this也放到arguments中呢？ 

因为Java。出于市场推广的考虑，JS在设计之初就是模仿Java的。 Java中有this，那么JS自然也要默认实现Java中this的用法。


## 3. 一个基本问题：this的值是什么时候确定的
抛开这个问题，先考虑函数fn()的参数a是什么时候确定的?
```
function fn(a){
   console.log(a);
}

```
当然是fn()被调用时传值给a的时候确定的。 `fn(1) //a就等于1` `fn(2)//a就等于2`

**所以说，我们可以说：参数的值只有在传参时可以确定。那么就有了推论：既然this也是一种参数， 那么this的值自然只有在传参时可以确定**

这句话看着挺无聊，但是很多人靠不清楚this的原因就在于：
1. 不会用call
2. 不知道this是个参数。在一些程序员眼里this好像是个神奇的常量一样，只不过这个常量在不同的情况下会变而已。

## 4. this 常见面试题
1. this为window对象
如果在浏览器中运行的话，结果就是window; 如果在nodejs中运行的话，结果就是global
```
function a(){
   console.log(this); 
}

a();  //window
```
2. 严格模式
使用严格模式的话，this就不会被瞎改了。所以结果为: undefined
```
function a(){
  'use strict'
   console.log(this);
}

a(); //undefined
```
3. 隐式传递this
使用`()`执行方法时，this会被隐式的传递进来。传递的参数就是方法前的对象，所以结果为： obj对象
```
var obj = {
   sayThis: a
}

obj.sayThis(); // obj对象
```
4. 用call() 显示传递this
call()的第一个参数就是this，传递什么，this就是什么
```
var obj = {
   sayThis: a
}

obj.sayThis.call(); //undefined;
obj.sayThis.call(1);//严格模式下是：基本类型1； 非严格模式下是：Number对象 {1}
```

5. 事件
按钮被点击时，会执行绑定的函数，`console.log(this)`的结果是button元素。
```
button.onclick = function(){
   console.log(this);
}

```
那问题来了：我怎么知道是button元素？这个值是由谁保证的？
点击按钮后调用事件处理函数是由浏览器来做的。既然这样，this的值显然就是由浏览器传递的。那么，浏览器会传递什么值给this呢？
答案是：查文档。文档保证了这个值就是button元素

> ![image.png](https://upload-images.jianshu.io/upload_images/9425951-a3b17aa02f7687ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6. JQuery 事件
```
$("#button1").on('click',function(){
    console.log(this);
 })
```
同问题5一样，我怎么知道this的值的呢？ 只有通过查Jquery文档才能知道。这里使用的是直接事件，所以this就是 button1
> ![image.png](https://upload-images.jianshu.io/upload_images/9425951-53765b78a2efe7f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面使用了事件代理。从文档可知，如果使用了事件代理的话，那么this就是 selector的元素。所以是: li 
```
$('#ul').on('click','li',function(){
     console.log(this);
})
```

**5,6两题看似无聊，其实只是为了强调this是个参数，在被传值之前是不知道究竟是什么的。除非你看文档**


## 5. 流传很广但是不好的this使用方式
```
$(’#button‘).on('click',function(){
     this.disabled = true;
   $.get('XXX',function(){
        console.log(this) //window
        this.disabled = false;
  })
})
```
上面代码的意图很简单：点击按钮后把button设置为不可用，执行`$.get()`的回调时再设置为可用。但问题是，`$.get()`的回调是不会传值给this的。所以，回调后this的值就是window，自然`this.disabled = false`是无效的

所以，有人给出了方案：
 ```
$(’#button‘).on('click',function(){
     this.disabled = true;
     var that = this; //用that存放一下this
   $.get('XXX',function(){
        console.log(this) //window
        that.disabled = false; //使用that
  })
})
```
很明显，就是在回调之前用一个变量`that`存储一下this，方便给后面用。可是这个变量名太怪了，还有人喜欢用_this来存储this，这就更绕了。 为什么不取个有意义的名字呢，比如这样： `var btn = this;`  一目了然。

## 6. JS禁用了this
什么？JS禁用了this？是的。
因为this的不确定性和不易使用，JS之父终于痛下决心把this给禁用了。禁用的方式就是通过**箭头函数**

```
var f = () => console.log(this)
f();// window
f.call(1) //windown
```
无论用什么方式（隐式的，或者显示的）给this传值，f()中的**this永远是window**
因为在箭头函数来说，这个this在定义方法的时候就被确定了：**如果f()的外面有this，那么就沿用外面的。如果没有，就不用。而且this不接受传参**。我们可以猜想，在JS中一定用有一个机制，禁用了call()一个箭头函数时，call()的第一个参数。 如:`f.call(1)`, JS发现f是箭头函数，在调用call()时就禁用了第一个参数。

我们来验证一下。
修改一下`f()`，让它接受一个参数。看看打印this和参数时会发生什么？
```
var f = (x) => console.log(this,x)
f.call(1,2);// window, 2
```
可以发现this还是window，然后参数依然是从call的第二位开始算起。那么，调用时传递的参数1呢？**1被JS忽略了**

有了箭头函数的话，第5点提到的问题就很好解决了。
```
$(’#button‘).on('click',function(){
     this.disabled = true; //line 1
     var f = () => {
           this.disabled = false;  // line 2    this就是上面的this
      }
    $.get('XXX', f);
})
```
但是要注意的是： **只有箭头函数中的this是可以确定的，普通函数中的this依然是不确定的**。 所以，line 1位置的this是在普通函数中，它依然不确定。


## 7. 总结
- 函数是独立于对象存在的
- 为了让对象与函数建立起关系，就要把对象传递给函数。所以，发明了this。给this传值有两种传递方式：隐式，显示（call）
- 隐式传递this时，传递的就是方法前的那个对象 
  ```
  obj.child.sayName(); //this: obj.child
  ```
-  显示方式传递this时，在非严格模式下，JS都会瞎改this。 所以一般都使用严格模式: 'use strict'
    ```
    var a = function(){console.log(this);}
     a.call(1) //传递的是基本类型1，但是变成了Number对象：{1} 
   ```
- 箭头函数中的this在定义函数时就是确定的。即使使用call来传递this，也会被JS忽略

