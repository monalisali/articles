## 1.@Mapper

@Mapper是让接口直接变成一个Mybatis mapper

### 1.1 不使用@Mapper的时候

接口：
```
public interface UserMapper {
    @Select("select * from user where id = #{id}")
    User getUserById(@Param("id")Integer id);
}
```
配置文件：
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

在一个Controller中要使用`getUserById()`

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
接口：
```
@Mapper
public interface UserMapper {
    @Select("select * from user where id = #{id}")
    User getUserById(@Param("id")Integer id);
}
```
配置文件：
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

在一个Controller中要使用`getUserById()`  
现在Mapper已经被@Mapper创建出来了，只要把它注入进来就可以用了。

```
  @Autowired
  UserMapper userMapper;
  
 @RequestMapping("/getUserById")
    public Object getUserById(@RequestParam("id")Integer id){
       return userMapper.getUserById(id);
    }
```

## 2. @Service





