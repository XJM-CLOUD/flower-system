# 使用说明
1.数据库配置
## 数据源基础配置
配置resources目录下application.yml文件中数据源基础配置，本项目使用mysql开发，
1.1在mysql数据库中建1个数据库，现配置文件中使用数据库为 hua，项目启动前需先在数据库建立一个数据库，将数据库配置改为自己
定义的数据库名
2.数据库用户名和密码改为自己的mysql数据库连接
3.本项目使用Spring-data-jpa作为数据源，支持的ORM框架为Hibernate,项目启动后自动创建表，但没有数据
可将resources目录下的import.sql文件导入数据库执行，作为初始化数据

2.项目启动
项目导入成功后，jar包下载完成，数据库参数配置好后，可直接启动程序
启动方式1：找到 src-main-java-目录下的文件 Application，右键直接运行 "Run Application",在控制台可看到输出日志
启动完成后，可在数据库中看到对应的表，在数据库中完成数据库初始化
在浏览器打开 http://localhost:34001/index或http://localhost:34001都可直接跳转到登陆页面登陆
登陆用户名：admin
密码： 123456


#关于spring boot项目的一些说明

## 项目结构介绍
Spring Boot的基础结构共三个目录:
```text
l、 src/main/java  程序开发以及主程序入口
2、 src/main/resources 配置文件
3、 src/test/java  测试程序
4、 src/test/resources 测试配置文件
```

root package结构：com.example.myproject
```text
com  
  +- example  
    +- myproject  
      +- Application.java  
      |  
      +- domain  
      |  +- Customer.java  
      |  +- CustomerRepository.java  
      |  
      +- service  
      |  +- CustomerService.java  
      |  
      +- web(controller) 
      |  +- CustomerController.java  
      |  
```
1、Application.java 建议放到跟目录下面,主要用于做一些框架配置
2、domain目录主要用于实体（Entity）与数据访问层（Repository）,本项目将domain划分为两个目录entity和dao目录
3、service 层主要是业务类代码
4、controller 负责页面访问控制      


## 项目配置文件 
  ### application.yml配置文件说明
  主要为启动spring boot项目的一些配置信息,如数据源配置
  
  #### 数据源配置
  show-sql 是否打印出自动生产的SQL，方便调试的时候查看
  dialect 主要是指定生成表名的存储引擎为InneoDB 
      jpa:
        database: mysql
        show-sql: true
        hibernate:
          ddl-auto: update
          naming:
            strategy: org.hibernate.cfg.DefaultComponentSafeNamingStrategy
        properties:
           hibernate:
              dialect: org.hibernate.dialect.MySQL5Dialect
  数据初始化
1.我们在做测试的时候经常需要初始化导入一些数据，如何来处理呢？
会有两种选择，一种是使用Jpa，另外一种是Spring JDBC。两种方式各有区别下面来详细介绍。
1)使用Jpa
在使用spring boot jpa的情况下设置spring.jpa.hibernate.ddl-auto的属性设置为 create or create-drop的时候，spring boot 启动时默认会扫描classpath下面（项目中一般是resources目录）
是否有import.sql，如果有机会执行import.sql脚本。
2)使用Spring JDBC 需要在配置文件中添加以下配置
```text
spring:
    datasource:
      schema: database/data.sql
      sql-script-encoding: utf-8
    jpa:
      hibernate:
        ddl-auto: none
```
spring boot项目启动的时候会自动执行脚本。

区别:第一种方式启动的时候Jpa会自动创建表，import.sql只负责创建表单后的初始化数据。
     第二种方式启动的时候不会创建表，需要在初始化脚本中加班判断表是否存在，不存在创建表再初始化脚本的步骤。

3)ddl-auto 四个值的解释
create： 每次加载hibernate时都会删除上一次的生成的表，然后根据你的model类再重新来生成新表，哪怕两次没有任何改变也要这样执行，这就是导致数据库表数据丢失的一个重要原因。
create-drop ：每次加载hibernate时根据model类生成表，但是sessionFactory一关闭,表就自动删除。
update：最常用的属性，第一次加载hibernate时根据model类会自动建立起表的结构（前提是先建立好数据库），以后加载hibernate时根据 model类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。
        要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等 应用第一次运行起来后才会。
validate ：每次加载hibernate时，验证创建数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值。 
none : 什么都不做。

#### thymeleaf 设置不校验html标签
官方默认配置
```text
#spring.thymeleaf.prefix=classpath:/templates/
#spring.thymeleaf.suffix=.html
#spring.thymeleaf.mode=HTML5
#spring.thymeleaf.encoding=UTF-8
# ;charset=<encoding> is added
#spring.thymeleaf.content-type=text/html
# set to false for hot refresh
spring.thymeleaf.cache=false
spring.thymeleaf.mode=LEGACYHTML5
```
可以看到其实上面都是注释了的，因为springboot会根据约定俗成的方式帮我们配置好。所以上面注释部分是springboot自动配置的，如果需要自定义配置，只需要修改上注释部分即可。 
spring.thymeleaf.cache=false表示关闭缓存，这样修改文件后不需要重新启动，缓存默认是开启的，所以指定为false。但是在intellij idea中还需要按Ctrl + Shift + F9. 

1.thymeleaf 设置不校验html标签
  默认配置下，thymeleaf对.html的内容要求很严格，比如，如果少最后的标签封闭符号/，就会报错而转到错误页。
  这样的html代码，也会被thymeleaf认为不符合要求而抛出错误。
  通过设置thymeleaf模板可以解决这个问题，下面是具体的配置:
```text
  spring.thymeleaf.cache=false
  spring.thymeleaf.mode=LEGACYHTML5
```
  LEGACYHTML5需要搭配一个额外的库NekoHTML才可用 
  项目中使用的构建工具是Maven添加如下的依赖即可完成(pom文件中):
  <dependency>
      <groupId>net.sourceforge.nekohtml</groupId>
      <artifactId>nekohtml</artifactId>
      <version>1.9.22</version>
  </dependency>
2.标签的使用，静态资源的引入
```text
 <link rel="stylesheet" th:href="@{/css/bootstrap.min.css}" />
```
文件所在位置是
static-css-bootstrap.min.css，springboot对于静态文件会自动查找/static public、/resources、/META-INF/resources下的文件。所以不需要加static.

## 缓存的配置
1.类中使用缓存方式:
设置好缓存配置之后我们就可以使用 @Cacheable 注解来缓存方法执行的结果
```text
// R  
@Cacheable("provinceCities")  
public List<City> provinceCities(String province) {  
    logger.debug("province=" + province);  
    return this.cityMapper.provinceCities(province);  
}  
比如根据省份名检索城市的 provinceCities 方法,加了缓存,缓存的key为provinceCities
```
2.缓存数据一致性保证
  CRUD (Create 创建，Retrieve 读取，Update 更新，Delete 删除) 操作中，除了 R 具备幂等性，其他三个发生的时候都可能会造成缓存结果和数据库不一致。为了保证缓存数据的一致性，在进行 CUD 操作的时候我们需要对可能影响到的缓存进行更新或者清除。
  对于CUD，可以使用@CacheEvict来清除缓存,若CUD操作返回的为实体，可以使用@CachePut 更新缓存策略
```text
// U  
@CacheEvict(value = { "provinceCities", "searchCity" }, allEntries = true)  
public int renameCity(String city_code, String city_name) {  
    City city = new City();  
    city.setCityCode(city_code);  
    city.setCityName(city_name);  
    this.cityMapper.renameCity(city);  
    return 1;  
}
本示例用的都是 @CacheEvict 清除缓存。
如果你的 CUD 能够返回 City 实例，也可以使用 @CachePut 更新缓存策略。能用 @CachePut 的地方就不要用 @CacheEvict，因为后者将所有相关方法的缓存都清理掉，比如上面方法被调用了的话，provinceCities 方法的所有缓存将被清除。
``` 
@CacheEvict是清除缓存的注解。其中注解参数可以只有value,key意思是清除在value值空间中的key值数据，此时默认在当前注解方法成功执行之后再清除。这时候就会存在一个问题，也许你的注解方法成功执行了删除操作，但是后续代码抛出异常导致未能清除缓存，下次查询时依旧从缓存中去读取，这时查询到的结果值是删除操作之前的值。
有一个简单的解决办法，在注解参数里面加上beforeInvocation为true，意思是说当执行这个方法之前执行清除缓存的操作，这样不管这个方法执行成功与否，该缓存都将不存在。
当注解参数加上allEntries为true时，意思是说这个清除缓存是清除当前value值空间下的所有缓存数据。

3.自定义缓存
```text
@Cacheable("users")  
public User findByUsername(String username)  
@Cacheable("users")  
public Integer getLoginCountByUsername(String username)  
```
@Cacheable 注解的方法，每个缓存的 key 生成策略默认使用的是参数名+参数值，
如上的方法的这个方法的缓存将保存于 key 为 users~keys 的缓存下，对于 username 取值为 "赵德芳" 的缓存，key 为 "username-赵德芳"。
一般情况下没啥问题，但如果 key 取值相等然后参数名也一样的时候就出问题了
 因此可以使用自定义的缓存
 解决办法是使用自定义缓存策略，对于同一业务(同一业务逻辑处理的方法，哪怕是集群/分布式系统)，生成的 key 始终一致，对于不同业务则不一致：
 ```text
 @Bean  
 public KeyGenerator customKeyGenerator() {  
     return new KeyGenerator() {  
         @Override  
         public Object generate(Object o, Method method, Object... objects) {  
             StringBuilder sb = new StringBuilder();  
             sb.append(o.getClass().getName());  
             sb.append(method.getName());  
             for (Object obj : objects) {  
                 sb.append(obj.toString());  
             }  
             return sb.toString();  
         }  
     };  
 } 
 ``` 
redis的缓存key为类名+方法名+方法参数

4.其他说明
要缓存的 Java 对象必须实现 Serializable 接口，因为 Spring 会将对象先序列化再存入 Redis
CacheManager 必须设置缓存过期时间，否则缓存对象将永不过期，这样做的原因如上，避免一些野数据“永久保存”。此外，设置缓存过期时间也有助于资源利用最大化，因为缓存里保留的永远是热点数据。
缓存适用于读多写少的场合，查询时缓存命中率很低、写操作很频繁等场景不适宜用缓存

## 缓存的配置2-用户登陆多次配置
实际中我们的权限信息是不怎么会改变的，所以我们希望是第一次访问，然后进行缓存处理,这里使用了shiro的缓存进行处理
1.主要分这么几个步骤：在pom.xml中加入缓存依赖；注入缓存；
```text
<!-- shiro ehcache -->  
        <dependency>  
            <groupId>org.apache.shiro</groupId>  
            <artifactId>shiro-ehcache</artifactId>  
            <version>1.2.3</version>  
        </dependency>  
 ``` 
 2.在ShiroConfig中配置缓存
 ```text
 /**  
     * shiro缓存管理器;  
     * 需要注入对应的其它的实体类中：  
     * 1、安全管理器：securityManager  
     */  
 @Bean  
    public EhCacheManager ehCacheManager(){  
       EhCacheManager cacheManager = new EhCacheManager();  
       cacheManager.setCacheManagerConfigFile("classpath:config/ehcache-shiro.xml");  
       return cacheManager;  
   
    }  
  //将缓存对象注入到SecurityManager中：
  securityManager.setCacheManager(ehCacheManager());
  
  ``` 
  3.添加缓存配置文件：
  在src/main/resouces/config添加ehcache-shiro.xml配置文件
  
  4.权限配置-->MyShiroRealm.doGetAuthorizationInfo()
    这个信息就只打印一次了，说明我们的缓存生效了
    
  5.添加类RetryLimitCredentialMatcher继承HashedCredentialsMatcher，从缓存中读取用户
  6.设置    HashedCredentialsMatcher hashedCredentialsMatcher = new RetryLimitCredentialMatcher(ehCacheManager());
