
## 创造new关键字的目的
创造new关键字的目的是批量创建对象

## 创建对象
创建一个对象
```
var solider = {
  id: 1000111, 
  age:55,
  walk:function(){},
  run:function(){ },
  eat:function(){ },
}

//传递solider给其他需要的方法
Barracks.create(solider)

```

创建100个对象
```
var 士兵们 = [];
for(var i=0; i<99;i++)
{
   var solider = {
     id: 1000111, 
     age:55,
     walk:function(){},
     run:function(){ },
     eat:function(){ },  
  }
  士兵们.push(solider);
}
  
//把士兵们[]传递给需要的方法
Barracks.batchCreate(士兵们);

```

这种做法显然不好，浪费了大量的内存，因为每个solider对象都有大量相同的内容。








