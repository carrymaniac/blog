# JSR107

Java Caching定义了5个核心接口，分别是CachingProvider, CacheManager, Cache, Entry 和 Expiry。

- CachingProvider定义了创建、配置、获取、管理和控制多个CacheManager。一个应用可以在运行期访问多个CachingProvider。
- CacheManager定义了创建、配置、获取、管理和控制多个唯一命名的Cache，这些Cache存在于CacheManager的上下文中。一个CacheManager仅被一个CachingProvider所拥有。
- Cache是一个类似Map的数据结构并临时存储以Key为索引的值。一个Cache仅被一个CacheManager所拥有。
- Entry是一个存储在Cache中的key-value对。
- Expiry 每一个存储在Cache中的条目有一个定义的有效期。一旦超过这个时间，条目为过期的状态。一旦过期，条目将不可访问、更新和删除。缓存有效期可以通过ExpiryPolicy设置。

![缓存结构如下](C:\Users\Lusty\Documents\assets\cache.png)

# Spring的缓存抽象

 Spring从3.1开始定义了org.springframework.cache.Cache
和org.springframework.cache.CacheManager接口来统一不同的缓存技术；
并支持使用JCache（JSR-107）注解简化我们开发；

 Cache接口为缓存的组件规范定义，包含缓存的各种操作集合；
Cache接口下Spring提供了各种xxxCache的实现；如RedisCache，EhCacheCache , ConcurrentMapCache等；

 每次调用需要缓存功能的方法时，Spring会检查检查指定参数的指定的目标方法是否已经被调用过；如果有就直接从缓存中获取方法调用后的结果，如果没有就调用方法并缓存结果后返回给用户。下次调用直接从缓存中获取。
使用Spring缓存抽象时我们需要关注以下两点；
1、确定方法需要被缓存以及他们的缓存策略
2、从缓存中读取之前缓存存储的数据



| 概念           | 解析                                                         |
| -------------- | ------------------------------------------------------------ |
| CacheManager   | 缓存管理器，管理各种缓存（Cache）组件                        |
| Cache          | 缓存接口，定义缓存操作。实现有：RedisCache、EhCacheCache、ConcurrentMapCache等 |
| @Cacheable     | 主要针对方法配置，能够根据方法的请求参数对其结果进行缓存     |
| @CacheEvict    | 清空缓存                                                     |
| @CachePut      | 保证方法被调用，又希望结果被缓存。                           |
| @EnableCaching | 开启基于注解的缓存                                           |
| keyGenerator   | 缓存数据时key生成策略                                        |
| serialize      | 缓存数据时value序列化策略                                    |



| 属性                            | 解释                                                         | 例子                                                         |
| ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| value                           | 缓存的名称，在 spring 配置文件中定义，必须指定至少一个       | 例如：@Cacheable(value=”mycache”) 或者 @Cacheable(value={”cache1”,”cache2”} |
| key                             | 缓存的 key，可以为空，如果指定要按照 SpEL 表达式编写，如果不指定，则缺省按照方法的所有参数进行组合 | 例如:@Cacheable(value=”testcache”,key=”#userName”)           |
| condition                       | 缓存的条件，可以为空，使用 SpEL 编写，返回 true 或者 false，只有为 true 才进行缓存/清除缓存，在调用方法之前之后都能判断 | 例如:@Cacheable(value=”testcache”,condition=”#userName.length()>2”) |
| allEntries (@CacheEvict )       | 是否清空所有缓存内容，缺省为 false，如果指定为 true，则方法调用后将立即清空所有缓存 | 例如：@CachEvict(value=”testcache”,allEntries=true)          |
| beforeInvocation (@CacheEvict)  | 是否在方法执行前就清空，缺省为 false，如果指定为 true，则在方法还没有执行的时候就清空缓存，缺省情况下，如果方法执行抛出异常，则不会清空缓存 | 例如： @CachEvict(value=”testcache”，beforeInvocation=true)  |
| unless (@CachePut) (@Cacheable) | 用于否决缓存的，不像condition，该表达式只在方法执行之后判断，此时可以拿到返回值result进行判断。条件为true不会缓存，fasle才缓存 | 例如：@Cacheable(value=”testcache”,unless=”#result == null”) |





## 如何开启缓存?

- 引入spring-boot-starter-cache模块
- @EnableCaching开启缓存
- 使用缓存注解:@Cacheable,@CacheEvict,@CachePut

Spring默认使用的是ConcurrentMapCacheManager==ConcurrentMapCache；将数据保存在   ConcurrentMap<Object, Object>中,开发中一般使用redis、memcached、ehcache；





## @Cacheable

```java
@Cacheable(value = {"emp"}/*,keyGenerator = "myKeyGenerator",condition = "#a0>1",unless = "#a0==2"*/)
    public Employee getEmp(Integer id){
        System.out.println("查询"+id+"号员工");
        Employee emp = employeeMapper.getEmpById(id);
        return emp;
    }
```

### 原理

1. 自动配置类；CacheAutoConfiguration

2. SpringBoot中集成了很多缓存的配置类：

   > org.springframework.boot.autoconfigure.cache.GenericCacheConfiguration
   >
   >org.springframework.boot.autoconfigure.cache.JCacheCacheConfiguration
   >
   >org.springframework.boot.autoconfigure.cache.EhCacheCacheConfiguration
   >
   >org.springframework.boot.autoconfigure.cache.HazelcastCacheConfiguration
   >
   >org.springframework.boot.autoconfigure.cache.InfinispanCacheConfiguration
   >
   >org.springframework.boot.autoconfigure.cache.CouchbaseCacheConfiguration
   >
   >org.springframework.boot.autoconfigure.cache.RedisCacheConfiguration
   >
   >org.springframework.boot.autoconfigure.cache.CaffeineCacheConfiguration
   >
   >org.springframework.boot.autoconfigure.cache.GuavaCacheConfiguration
   >
   >org.springframework.boot.autoconfigure.cache.SimpleCacheConfiguration【默认】
   >
   >org.springframework.boot.autoconfigure.cache.NoOpCacheConfiguration

3.  给容器中注册了一个CacheManager：ConcurrentMapCacheManager

4. 可以获取和创建ConcurrentMapCache类型的缓存组件；他的作用将数据保存在ConcurrentMap中；

### 运行流程

1、方法运行之前，先去查询Cache（缓存组件），按照`cacheNames`指定的名字获取；（CacheManager先获取相应的缓存），第一次获取缓存如果没有Cache组件会自动创建。

2、去Cache中查询缓存的内容（使用Key进行查询即key-value）,默认的key就是方法的参数；

> key是按照策略生成的，默认的策略是keyGenerator，而keyGenerator默认使用的是SimpleKeyGenerator来生成Key，而SimpleKeyGenerator生产key的默认策略为：
>
> 如果没有参数；key=new SimpleKey()；
>
> 如果有一个参数：key=参数的值
>
> 如果有多个参数：key=new SimpleKey(params)；

3、没有查到缓存就调用目标方法

4、将目标方法查询的结果放进缓存中。用于下次查询

### 几个属性

cacheNames/value：指定缓存组件的名字;将方法的返回结果放在哪个缓存中，是数组的方式，可以指定多个缓存；

-  key：缓存数据使用的key；可以用它来指定。默认是使用方法参数的值  
  - 1-方法的返回值
  - 编写SpEL； #id;参数id的值   #a0  #p0  #root.args[0] getEmp[2]

-  keyGenerator：key的生成器；可以自己指定key的生成器的组件id​       
- key/keyGenerator：二选一使用;

-  cacheManager：指定缓存管理器；或者cacheResolver指定获取解析器

-  condition：指定符合条件的情况下才缓存：
  - condition = "#id>0" 
  - condition = "#a0>1"：第一个参数的值》1的时候才进行缓存

- unless:否定缓存；当unless指定的条件为true，方法的返回值就不会被缓存；可以获取到结果进行判断；
  - unless = "#result == null"； 
  - unless = "#a0==2":如果第一个参数的值是2，结果不缓存；
- sync：是否使用异步模式

## @CachePut

既调用方法，又更新缓存数据；同步更新缓存；修改了数据库的某个数据，同时更新缓存；一般用于更新数据库操作。

一般使用该注解需要设定好key值，因为一般和查询使用的key值不相同，查询使用的key值默认为参数即ID，而更新使用的参数默认也是参数但是为对象，因此需要手动设定为参数.id(#employee.id)，或者是#result.id。`其中，Cacheable不能使用result，因为该注解在方法执行前进行，result为空`

```JAVA
    @CachePut(value = "emp",key = "#result.id")
    public Employee updateEmp(Employee employee){
        System.out.println("updateEmp:"+employee);
        employeeMapper.updateEmp(employee);
        return employee;
    }
```

### 运行时机

1. 先调用目标方法
2. 将目标的结果进行缓存



## @CacheEvict

@CacheEvict：缓存清除

### 参数:

- allEntries = true：指定清除这个缓存中所有的数据

- beforeInvocation = false：缓存的清除是否在方法之前执行;默认代表缓存清除操作是在方法执行之后执行;如果出现异常缓存就不会清除

- beforeInvocation = true: 代表清除缓存操作是在方法运行之前执行，无论方法是否出现异常，缓存都清除

```java
@CacheEvict(value="emp",beforeInvocation = true/*key = "#id",*/)
    public void deleteEmp(Integer id){
        System.out.println("deleteEmp:"+id);
        //employeeMapper.deleteEmpById(id);
        int i = 10/0;
    }
```



## @Caching

自定义缓存规则：

```java
@Caching(
         cacheable = {
             @Cacheable(/*value="emp",*/key = "#lastName")
         },
         put = {
             @CachePut(/*value="emp",*/key = "#result.id"),
             @CachePut(/*value="emp",*/key = "#result.email")
         }
    )
    public Employee getEmpByLastName(String lastName){
        return employeeMapper.getEmpByLastName(lastName);
    }
```





# 使用Redis做缓存

默认保存数据 k-v 都是Object；利用java的序列化保存：

1、引入了redis的starter，cacheManager变为 RedisCacheManager；

 2、默认创建的 RedisCacheManager 操作redis的时候使用的是 RedisTemplate<Object, Object>

3、RedisTemplate<Object, Object> 是 默认使用jdk的序列化机制

如果想要用json进行序列化，需要自定义CacheManager；

## 步骤

1. 导入starter：spring-boot-starter-data-redis
2. 配置redis地址（在application.yml或者是properties配置）
3. 使用ReditTemplate操作Redis
4. 配置序列化器

## 配置redis

在properties中加入：`spring.redis.host=118.24.44.169`

## 配置RedisTemplate

```java
@Bean
public RedisTemplate<Object, Employee> empRedisTemplate(
RedisConnectionFactory redisConnectionFactory) 
    throws UnknownHostException {
RedisTemplate<Object, Employee> template = new RedisTemplate<Object, Employee>();
template.setConnectionFactory(redisConnectionFactory);
    //实例化序列化器，这里配置的是JSON的
Jackson2JsonRedisSerializer<Employee> ser = new Jackson2JsonRedisSerializer<Employee>(Employee.class);
//配置到Template中
 template.setDefaultSerializer(ser);
return template;
    }
```

## 配置CacheManager

RedisCacheManager 帮我们创建 RedisCache 来作为缓存组件；RedisCache通过操作redis缓存数据的

CacheManagerCustomizers可以来定制缓存的一些规则

```java
@Primary  //如果有多个缓存管理器，需要将某个缓存管理器作为默认的
@Bean
public RedisCacheManager employeeCacheManager(RedisTemplate<Object, Employee> empRedisTemplate){
    //实例化一个RedisCacheManager，并且将Template配置好
RedisCacheManager cacheManager = new RedisCacheManager(empRedisTemplate);
//使用前缀，默认会将CacheName作为key的前缀
cacheManager.setUsePrefix(true);
        return cacheManager;
    }
```

