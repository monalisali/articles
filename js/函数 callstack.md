## 什么是函数的callstack
在数据机构中，stack就是先进后出的意思。那么stack放到函数调用中，它的含义也是一样的。


## 示例

```
funciton a(){
  console.log('a'); // line 1
  return 'a'
}

function b(){
   console.log('b'); //line 2
   return 'b'
}

functon c(){
  console.log('c'); //line 3
  return 'c'
}

a(); //line 4
b(); //line 5
c(); //line 6

```
在执行a()的顺序是：
1. a() 执行，**记住位置6**
2. 进入存放在内存中的funtion a()函数体
3. console.log('a') 执行， **记住位置1**
4. 进入console.log()的函数体
5. console.log()执行完毕，**回到位置1**
6. return 'a'
7. 发现funtion a()已经执行完毕了，**回到位置4**
8. a()执行完成
9. 开始执行b().........

所以说JS在执行函数调用时会记住函数执行时的位置，当函数执行完成后再回到刚才记住的位置，然后再执行后续的函数。存放“位置信息”的那个地方就是callstack

可以使用这个站点来查看JS函数的callstack： http://latentflip.com/loupe


## 递归的callstack
```
function fab(n){
   console.log('start calc fab ' + n);
   if(n>3){
      return fab(n-1) + fab(n-2);
   }
   else{
      return 1
   }
}

fab(5)

```
