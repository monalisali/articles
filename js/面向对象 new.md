
## 创造new关键字的目的
创造new关键字的目的是批量创建对象

## 创建对象
#### 创建一个对象
```
var solider = {
  id: 1000111, 
  type: '中国海军',
  walk:function(){ },
  run:function(){ },
  eat:function(){ },
}

//传递solider给其他需要的方法
Barracks.create(solider)

```

#### 创建100个对象
```
var soliders = [];
for(var i=0; i<99;i++)
{
   var solider = {
     id: i, 
     type: '中国海军',
     walk:function(){},
     run:function(){ },
     eat:function(){ },  
  }
  soliders.push(solider);
}
  
//把soliders数组传递给需要的方法
Barracks.batchCreate(soliders);

```

很简单啊

#### 缺陷
上面的写法显然不好，每个solider对象都有大量相同的内容，这样就浪费了大量的内存。如：每个对象都有walk,run,eat函数，也有相同的type："中国海军"。创建100个对象的话就等于创建了300个函数和100个type了。

#### 修改

1. 利用原型链存放共用对象

可以使用原型链来解决重复创建对象的问题。我们可以创建一个**共用对象**：SoliderCommon 来存放公用的内容。然后让每个solider对象的__proto__指向SoliderCommon就可以了。

```
var soliderCommon = {
   type: '中国海军', //不是只有函数才可以放到共有对象中，属性也是可以的
   walk:function(){},
   run:function(){ },
   eat:function(){ }, 
};


var soliders = []
for(var i=0; i<100; i++){
  var solider = {
    id: i, 
  }
   
  /* !!!!!! 实际工作中千万不要这样写，因为 __proto__ 不是标准属性。这里只是为了说明问题而已*/
  solider.__proto__ = soliderCommon 
  soliders.push(solider)
}

var id = soliders[0].id; //id是每个solider特有的
soliders[0].walk(); // 第0个士兵有walk函数
soliders[1].walk();// 第1个士兵也有walk函数

//把soliders数组传递给需要的方法
Barracks.batchCreate(soliders);

```

这样创建的话，每个solider有自己的id，并且共享**共用**对象：soliderCommmon。 显然，这样就大大节省了内存空间。 
从省内存的角度来看，这种优化已经很完美了。但是从代码结构来看，还是有点松散，没有组织。


2. 利用构造函数使代码更紧凑一点

使用构造函数可以封装创建士兵的细节，在需要solider对象的时候只要调用CreateSolider函数即可。
```
var soliders = [];
var soliderCommon = {
   type: '中国海军', //不是只有函数才可以放到共有对象中，属性也是可以的
   walk:function(){},
   run:function(){ },
   eat:function(){ }, 
};

function CreateSolider(i){
   var solider = {
    id: i, 
  }
  solider.__proto__ = soliderCommon;
  return solider;
}

for(var i=0; i<99; i++){
   soliders.push(CreateSolider(i));
}

Barracks.batchCreate(soliders);

```
优化到这里，已经很完美了。唯一缺陷就是：**代码太分散了。** 分散表现在：CreateSolider函数是依赖于soliderCommon对象的。没有soliderCommon对象的话，CreateSolider函数就会报错。但是，它们两者之间除了名字都带有solider以外，没有任何联系。在代码很多的情况下，soliderCommon对象是很有可能被人不小心移走的。

所以，我们应该想办法让 CreateSolider函数和soliderCommon对象联系的更紧密一点。

3. 把共用对象soliderCommon作为构造函数CreateSolider的一个属性

```
var soliders = [];
//在构造函数中定义了共用属性: xxx
CreateSolider.xxx = {
   type: '中国海军', //不是只有函数才可以放到共用对象中，属性也是可以的
   walk:function(){},
   run:function(){ },
   eat:function(){ }, 
};

function CreateSolider(i){
   var solider = {
    id: i, 
  }
  solider.__proto__ = CreateSolider.xxx;
  return solider;
}

for(var i=0; i<99; i++){
   soliders.push(CreateSolider(i));
}

Barracks.batchCreate(soliders);

```

我们把共用对象(原来叫soliderCommon, 现在叫xxx)定义在了构造函数CreateSolider中。这样的话，构造函数CreateSolider和共用对象的联系就紧密了，因为共用属性就定义在CreateSolider内。

代码终于完美了。


## 来自JS之父的关怀

上文第3点这种写法很好，但是每次创建一个构造函数都要做类似的事情：
1.定义一个xxx对象作为共用对象
2.要把创建出的对象（如：示例中的obj）的__proto__指向 xxx
3.最后还要返回创建的对象

JS之父看到了这个问题，于是它做了两件事：
1.规定xxx叫做：prototype
2.发明了关键字: new。 使用new后：
-- 不用再为临时对象想名字了（上方示例中的obj），用this代替
-- 不用把临时对象的__proto__指向 xxx
-- 不用在构造函数中返回临时对象了

代码可以改成：
```
var soliders = [];
function CreateSolider(i){ 
    this.id = i;
}

/* 
/*创建CreateSolider函数后，就自动有了CreateSolider.prototype对象。prototype对象内的一些属性也会被自动赋值，如：constructor会被自动赋值为CreateSolider*/
CreateSolider.prototype = {
  constructor: CreateSolider
}
*/


//CreateSolider.prototype指向了一个新的对象，需要把原先对象的数据给补录进来，如：constructor属性的值
CreateSolider.prototype = {
   constructor: CreateSolider, //补录，重新为constructor赋值
   type: '中国海军', //不是只有函数才可以放到共用对象中，属性也是可以的
   walk:function(){},
   run:function(){ },
   eat:function(){ }, 
};

for(var i=0; i<99; i++){
   soliders.push(new CreateSolider(i));
}

Barracks.batchCreate(soliders);


```
定义CreateSolider函数后，就自动有了CreateSolider.prototype对象。CreateSolider.prototype对象中的某些值也会被自动赋值，如:constructor会被自动赋值为CreateSolider。 

所以针对CreateSolider.prototype的操作都要写在构造函数定义之后。如果像下面这样写的话，定义CreateSolider函数后CreateSolider.prototype就会被覆盖，type,walk,run等内容就都没有了。

```
CreateSolider.prototype = {
   constructor: CreateSolider, //重新为constructor赋值
   type: '中国海军', //不是只有函数才可以放到共用对象中，属性也是可以的
   walk:function(){},
   run:function(){ },
   eat:function(){ }, 
};

function CreateSolider(i){ 
    this.id = i;
}

```
反过来讲，当我们用正确的顺序来创建`CreateSolider.prototype = {......}` 共用对象时，又会覆盖掉原先的CreateSolider.prototype对象。而这个原先的对象中有些值已经被赋值了，如：constructor属性。所以，要有一个补录的操作。


**更推荐的写法**

更安全的写法是在现有`CreateSolider.prototype`对象上添加属性，这样就不必补录数据了。
```
var soliders = [];

function CreateSolider(i){ 
    //new关键字自动： this = {};
    //new关键字自动： this.__proto__ = CreateSolider.prototype;
    this.ID = i;
    
    //new关键字自动： return this;
}

//直接在现有的CreateSolider.prototype对象上添加
CreateSolider.prototype.type: '中国海军'; //不是只有函数才可以放到共用对象中，属性也是可以的
CreateSolider.prototype.walk:function(){};
CreateSolider.prototype.run:function(){ };
CreateSolider.prototype.eat:function(){ };

for(var i=0; i<99; i++){
   soliders.push(new CreateSolider(i));
}

Barracks.batchCreate(soliders);

```

## 总结
1. 创建对象最好使用构造函数
2. new关键字自动的为我们做了一些事情
   - 创建了一个临时对象，用this来表示
   - 自动return构造函数创建的对象
   - 把对象的__proto__指向构造函数的prototype对象
3. **对象的__proto__属性指向其构造函数的prototype对象**






