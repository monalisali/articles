## Collection体系原理与常用实现

> 类图
![图](/resource/connection类图.png)



在Java中没有直接对Collection接口进行实现，但对它的子接口有大量的实现。 而在这些子接口中，最重要的就是List和Set。 
### 1 Collection：List
List的特点是：允许元素重复，但元素是有序的

#### 1.1 声明
下面两种方式都可以，只是第一种方式用了List接口而已。
```java
List<Integer> list = new ArrayList<Integer>();
ArrayList<Integer> list = new ArrayList<Integer>();
```

#### 1.2 ArrayList如何进行动态扩容
我们知道ArrayList可以用`add()` 来添加元素，而且貌似可以无限添加，那Java是如何做到这种无限添加元素的呢？
奥秘就在这个 `grow(int minCapacity)` , 它就是把老的长度 + 老的长度/2  (`oldCapacity + (oldCapacity >> 1);` , `oldCapacity >> 1` 的效果就是除以2 )。 然后再把元素copy到，新长度的空间中去。
```java
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```


### 2 Collection: Set
Set的特点是：不允许元素有重复，元素是无序的。

#### 2.1 JAVA世界中第一重要的约定：equals() 
   equal()用来判断对象是否重复 
   
#### 2.2 JAVA世界中第二重要的约定: hashCode
hashCode就是一个对象的哈希值。从Object类的hashCode上的注释可以知道，它可以使那些支持hash table的数据结构收益，如：HashMap
假设现在我们要往一个Collection中添加一个元素，但添加之前需要先判断一下这个元素是否已经存在。
如果用List实现的话，查询的过程会遍历List中**所有的**元素。但用Set(如HashSet)的话，JDK只会到相应的哈希桶里面找，这个过程会快很多。

以添加人名为例，List的流程就是：在添加王五之前，会把List中所有的元素遍历一遍，即6个元素。

![List添加元素](/resource/list.png)


如果要**HashSet**存放元素的话，同样姓氏的元素会被存放在**同一个哈希桶**里面。在添加王五时，就只需遍历王姓的那个哈希桶就可以了，即1个元素，效率显然就高很多。
![Set添加元素](/resource/set.png)


可以把hashCode当做对象的类型代码，相同的hashCode会存放在同一个哈希桶中。**hashCode有如下重要特性：**
- 同一个对象必须始终返回同样的hashCode
- 两个对象的equals()返回true，必须返回相同的hashCode
- 两个对象不相等，也有可能返回相同的hashCode。从这句话也可以推断出： **hashCode不是对象的唯一标识，不能用它来作为对象的唯一键来使用**
- Object类默认的equals(), 判断两个对象相等的条件是：它们的reference指向同一块内存。（Object class equals() method implementation returns true only when both the references are pointing to same object.）

最佳实践
- equlas() 和 hashcode()中用到的属性，就是用来生成hashcode和比较是否相等的依据。 如果一个属性的值更新了，那么很有可能它的hashcode就变了。
- 实现equals()和hashcode()时，最好使用相同的属性。这样当属性有更新时，它们就不会相互冲突了。（Use same properties in both equals() and hashCode() method implementations, so that their contract doesn’t violate when any properties is updated.）

[equals(), hashcode 参考](https://www.journaldev.com/21095/java-equals-hashcode#java-equals)


```java
   
   public class Person {
    /** 身份证号 */
    private final String id;
    /** 姓名 */
    private String name;
    /** 年龄 */
    private int age;
    
   public Person(String id) {
        this.id = id;
    }

    public Person(String id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
    
    //getter setter ......
    
    
    //同时使用了：id, age, name 来作为生成hascode和equlas判断的条件
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return Objects.equals(id, person.id)
                && age == person.age
                && Objects.equals(name, person.name);
    }


    @Override
    public int hashCode() {
        return Objects.hash(id,age,name);
    }
    
 }
   
 //Test代码  
   
Assertions.assertEquals(new Person("1"), new Person("1"));  //pass: id相等，age相等（都为0），name相等（都为空）。期待的是true，返回的也是true
Assertions.assertNotEquals(new Person("1"), new Person("2")); //pass:  id不相等。期待的是false，返回的也是false

Person person = new Person("1");
person.setAge(20);
Assertions.assertEquals(person, new Person("1")); //failed: id相等，age不相等。 期待的是true，返回的是false

  
```


```java

 //如果把equals()和hashCode中属性只使用id的话，上面的Test代码就都pass了
 @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return Objects.equals(id, person.id);
    }


    @Override
    public int hashCode() {
        return Objects.hash(id);
    }

```




在上面这个例子中，**姓氏**就是各个对象的hashCode，有相同姓氏的姓名数据被存放到同一个姓氏类别中了(**哈希桶**)。这样的话，在判断王五是否存时，只要通过它的*姓氏(hashCode)* 定位到对应的 *王姓* 那个哈希桶，然后在这个桶里面进行遍历，这样当然比遍历所有集合中的元素快得多了。**我们也可以把hashCode理解为Category Code, 可以用来给对象进行分组。**


#### 2.3 LinkedHashSet 中的元素顺序与数据被插入的数据是一致的


### 3 Map<key,value> 接口
Map<key,value> 接口就是键值对数据结构

#### 3.1 声明
`Map<String,String> map = new HashMap<>();`

#### 3.2 常用方法
- put()
- putAll()
- get()
- size()
- containsKey()
- containsValue()
- keySet()：返回的是Set，因为key不可重复
- values()：返回的是Collection，因为value可以重复
- entrySet()
- remove()
- clear()

#### 3.3 keySet()有坑
keySet() 返回的内容是会随着Map中key的改变而改变的。 如下所示，map变量中原来有2个元素，getKeys的结果自然也是2个元素。**但从map中移除一个元素后，getKeys变量的的结果也自动改变了。**
```java
Map<String,String> map = new HashMap<>();
map.put("A","AAA");
map.put("B","BBB");
Set<String> getKeys = map.keySet();
map.remove("A");

```

#### 3.4 HashMap是如何扩容的？
创建一个更大的HashMap，然后把数据copy进去。可以看下HashMap中put()-->putVal()的实现，注意在倒数的地方有个`resize();` 扩容的操作都在里面了。

#### 3.5 HashMap是线程不安全的。
在多线程的环境下，HashMap在扩容时可能会造成死循环，从而导致CPU100% 。应该使用ConcurrentHashMap

#### 3.6 HaspMap在 Java 7+后的改变：链表变成了红黑树。修改的原因是：如果特别巧或者精心构造很多hashCode相同的对象的话，这些对象就会被放到同一个哈希桶中，这样就丧失了hash的优势（变成List了），这样就会造成性能问题。

#### 3.7 HashMap和HashSet本质上是同一种东西
   HashMap中key的Set就是一个HashSet，二期HashSet的实现中用的就是HaspMap
   


### 4 TreeSet，TreeMap

#### 4.1 几种Set的特点 
Set接口主要有3中实现：HashSet，LinkedHashSet，TreeSet。 它们的特点是：
- HashSet： 元素的顺序是完全随机的
- LinkedHashSet： 元素的顺序于元素被插入时的顺序保持一致
- TreeSet： 可以对元素进行排序，默认是从小到大排序。TreeSet有个构造函数可以接受Comparator


同样的Map也有几种实现：HashMap, LinkedHashMap, TreeMap. 它们的特点与上面说的Set说一样的。

#### 4.2 二叉树/红黑树简介
TreeSet中的查询和插入元素，使用了二叉树。二叉树中一个元素的左面子元素一定比它小，右面的子元素一定比它大。
如下图所示，要查找 1 2 3 4 5 6。 如果用ArrayList来存放，查找4就是要找4次，查找6就要找6次。如果用TreeSet来存放，查找4只要3次，查找6页只要3次。

![二叉树](/resource/二叉树.png)

### 5 Collections工具方法集合
**在Java世界中有个约定，一个类的工具类的实现，一般后面加个s，如：Collection的工具类就是Collections”
- emptySet()
- synchronizedCollection
- unmodifiableCollection

### 6 Collection的其他实现
1. Queue/Deque
2. ~~Vector/Stack~~ （Java已经不推荐使用了，有更好的实现）
3. LinkedList
4. ConcurrentHashMap
5. PriprityQueue

#### 7 Guava
对于Collections的一个很好的补充，如果发现Collections不能满足你的需求，可以到Guava中看一下
- Lists/Sets/Maps
- ImmutableMap/ImmutableSet
- Multiset/Multimap
- BiMap









  
