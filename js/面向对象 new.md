
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
  
//把士兵们[]传递给需要的方法
Barracks.batchCreate(soliders);

```

很简单啊

#### 缺陷
上面的写法显然不好，浪费了大量的内存，因为每个solider对象都有大量相同的内容。如：每个对象都有walk,run,eat函数，也有相同的type："中国海军"。创建100个对象的话就等于创建了300个函数和100个type了。

#### 修改

1. 利用原型链存放共用对象
可以使用原型链来解决重复创建对象的问题。我们可以创建一个**共用的对象**：SoliderCommon 来存放公用的内容，然后让每个solider对象的__proto__指向SoliderCommon就可以了。

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
    ID: i, 
  }
   
  /* !!!!!! 实际工作中千万不要这样写，因为 __proto__ 不是标准属性。这里只是为了说明问题而已*/
  solider.__proto__ = soliderCommon 
  soliders.push(solider)
}

var id = soliders[0].id;
soliders[0].walk(); // 第0个士兵有walk函数
soliders[1].walk();// 第1个士兵也有walk函数

//把士兵们[]传递给需要的方法
Barracks.batchCreate(soliders);

```

这样创建的话，每个solider有自己的id，并且共享**共用**对象：soliderCommmon。 显然，这样就大大节省了内存空间。 
从省内存的角度来看，这种优化已经很完美了。但是从代码结构来看，还是有点松散，没有组织。


2. 利用构造函数使代码更紧凑一点

使用构造函数可以封装创建士兵的细节。
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
    ID: i, 
  }
  solider.__proto__ = soliderCommon;
  return solider;
}

for(var i=0; i<99; i++){
   soliders.push(CreateSolider(i));
}

Barracks.batchCreate(soliders);

```
优化到这里，已经很完美了。遗留的唯一缺陷就是：**代码太分散了。** 分散的具体表现在：CreateSolider函数是依赖于soliderCommon对象的，没有soliderCommon对象的话，CreateSolider函数就会报错。但是，它们两者之间除了名字都带有solider以外，没有任何联系。在代码很多的情况下，soliderCommon对象是很有可能被人不小心移走的。

所以，我们应该想办法让 CreateSolider函数和soliderCommon对象联系的更紧密一点

3. 把共用属性作为构造函数的一个属性

```
var soliders = [];
//在构造函数中定义了共用属性XXX
CreateSolider.xxx = {
   type: '中国海军', //不是只有函数才可以放到共用对象中，属性也是可以的
   walk:function(){},
   run:function(){ },
   eat:function(){ }, 
};

function CreateSolider(i){
   var solider = {
    ID: i, 
  }
  solider.__proto__ = CreateSolider.xxx;
  return solider;
}

for(var i=0; i<99; i++){
   soliders.push(CreateSolider(i));
}

Barracks.batchCreate(soliders);

```

我们把共用对象(原来叫soliderCommon, 现在叫xxx)定义在了构造函数CreateSolider中。这样的话，构造函数CreateSolider和共用对象联系就紧密了，因为共用属性就定义在CreateSolider内。

**xxx在JS被叫做：prototype 所以，上面这个代码可以表述为：对象的__proto__属性指向其构造函数的prototype对象**










