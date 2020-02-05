**首先要明确一点：只有Spring容器可以正确的通过@Mapper和@Spring来创建对象。如果在非Spring容器中，既是在Java类上使用了这两个注解，并在需要的地方注入了这些Java类的对象，在运行时会报null错误。比如说，main函数不是一个Spring容器。**  可以看下[github generalTest项目](https://github.com/monalisali/general-test) 中SpringMain中的注释


## 1.@Mapper

@Mapper针对的是：MyBatis的**接口 + 注解**使用方式，它可以让接口直接变成一个Mybatis Mapper，从而减少代码。

### 1.1 不使用@Mapper的时候

1. 接口
```
public interface UserMapper {
    @Select("select * from user where id = #{id}")
    User getUserById(@Param("id")Integer id);
}
```

2. 配置文件
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
 <!--这个enviroment配置是必须的，不然在SqlSessionFactory sqlSessionFactory =new SqlSessionFactoryBuilder().build(inputStream);
        会报错，因为builder方法在调用时需要一个enviroment参数
        -->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="org.h2.Driver"/>
                <property name="url" value="jdbc:h2:file:C:\javaWorks\my-first-spring/target/test"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>       
        <mapper class="xdml.dao.UserMapper" />
    </mappers>
</configuration>
```

3. 在一个Controller中要使用`getUserById()`

配置文件虽然知道有一个UseMapper接口是用来做MyBatis Mapper的，但MyBatis并不会自动把这个Mapper创建出来，所以得你自己创建。需要先读取配置文件，
然后再创建SqlSession对象，再创建UserMapper对象后, 才可以调用`getUserById()`
```
 @RequestMapping("/getUserById")
    public Object getUserById(@RequestParam("id")Integer id){
        String resource = "db/mybatis/config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            e.printStackTrace();
        }
        SqlSessionFactory sqlSessionFactory =
                new SqlSessionFactoryBuilder().build(inputStream);

        try (SqlSession session = sqlSessionFactory.openSession()) {
            System.out.println("***********动态代理******************");
            UserMapper mapper = session.getMapper(UserMapper.class);
            return mapper.getUserById(id);
        }
    }
```

### 1.2 使用@Mapper的时候
使用@Mapper后1.1中的代码可以简化很多，@Mapper直接就把UserMapper Interface变成了MyBatis Mapper

1. 接口
```
@Mapper
public interface UserMapper {
    @Select("select * from user where id = #{id}")
    User getUserById(@Param("id")Integer id);
}
```

2. 配置文件：
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <mappers>       
        <mapper class="xdml.dao.UserMapper" />
    </mappers>
</configuration>
```

3. 在一个Controller中要使用`getUserById()`  
现在Mapper已经被@Mapper创建出来了，只要把它注入进来就可以用了。而且现在不需要自己创建Mapper了，配置文件也少了很多。

```
  @Autowired
  UserMapper userMapper;
  
 @RequestMapping("/getUserById")
 public Object getUserById(@RequestParam("id")Integer id){
    return userMapper.getUserById(id);
 }
```

## 2. @Service

@Service针对的是：MyBatis的**XML配置文件**使用方式，它可以让接口直接变成一个Java Bean，从而减少代码。

### 2.1 不使用@Service的使用

1. OrderDao.java
```
package Spring;

public class OrderDao {
    public void select(){
        System.out.println("select!");
    }
}

```

2. OrderService.java
```
package Spring;
import org.springframework.beans.factory.annotation.Autowired;

public class OrderService {
    //@Autowired 告诉Spring, 这个OrderDao对象是要自动装配的
    @Autowired
    private OrderDao orderDao;

    public void doSomething(){
        orderDao.select();
        System.out.println("user service do something");
    }
}

```

3. 配置文件
resources目录下的config.xml文件，通过<bean>配置项来告诉Spring哪些Java对象是要当做Bean来处理的。
    
```
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd ">

    <!--有了这个配置，Spring才能识别注解，如:@Autowired-->
    <context:annotation-config/>

    <!--bean在Spring中就是Java对象，这里的bean配置就是告诉Spring，有哪些Java对象需要处理-->
    <bean id="orderDao" class="Spring.OrderDao"/>
    <bean id="orderService" class="Spring.OrderService"/>
</beans>
```

4. 使用
虽然配置文件中配置了Bean，当Spring并不会自动把这些对象变成Java Bean。所以需要通过配置文件来获取<bean>的配置项，再创建出Java Bean(OrderService)后才能正常使用`doSomething()`
```
 public void OrderServiceDoSomething(){
   //BeanFactory就是Spring容器
   BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:config.xml");
   OrderService orderService = (OrderService)beanFactory.getBean("orderService");
   orderService.doSomething();
 }
 ```

### 2.2使用@Service时

1. OrderDao.java
```
package Spring;

@Service
public class OrderDao {
    public void select(){
        System.out.println("select!");
    }
}

```

2. OrderService.java
```
package Spring;
import org.springframework.beans.factory.annotation.Autowired;

@Service
public class OrderService {
    //@Autowired 告诉Spring, 这个OrderDao对象是要自动装配的
    @Autowired
    private OrderDao orderDao;

    public void doSomething(){
        orderDao.select();
        System.out.println("user service do something");
    }
}

```
3. 使用
@Service已经把OrderDao和OrderService变为JavaBean了，所以就不需要配置文件了，直接把OrderService注入进来就可以使用了。
这里说的可以不要的那个配置文件指的是：写<bean>配置的那个文件，而不是写sql语句的配置文件。其实应该有一个配置文件来写sql，然后在OrderDao的`doSomething()`中调用，这里为了简单就没有写这个配置文件。
    
```
 @Autowired
 OrderService orderService;
 
 public void OrderServiceDoSomething(){
   orderService.doSomething();
 }
 ```

