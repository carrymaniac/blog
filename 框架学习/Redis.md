| 数据类型            | 数据类型存储得值               | 说明                         |
| ------------------- | ------------------------------ | ---------------------------- |
| STRING(字符串)      | 保存字符串、整数和浮点数       | 可以对字符串进行操作         |
| LIST（列表）        | 链表                           | 支持链表两端插入或者弹出节点 |
| SET（集合）         | 无序收集器                     |                              |
| HASH（哈希散列表）  | 类似于java中的map              |                              |
| ZSET（有序集合）    | 有序集合，通过分值的大小来决定 |                              |
| HyperLogLog（基数） | 用于计算重复的值               | 只提供基数的计算             |



##  Redis命令

`Redis清空命令：flushdb`

### STRING基本命令

|          命令          |                    说明                     |                    备注                    |
| :--------------------: | :-----------------------------------------: | :----------------------------------------: |
|     set key value      |                 设置键与值                  |                                            |
|        get key         |                通过键获取值                 |                                            |
|        del key         |                通过键删除值                 |    通用的命令，在其他数据结构亦可以使用    |
|    getset key value    |       修改原来key指向的值，并返回旧值       |            若原值为空则返回为空            |
|       strlen key       |             求key指向的值的长度             |                  返回长度                  |
| getrange key start end |            获取key指向的值的子串            | 字符串长度为len start与end的取值为0到end-1 |
|    append key value    | 把新的字符串value加入到原来的指定字符串末尾 |         返回key指向的新字符串长度          |
|                        |                                             |                                            |

`关于在Spring对Redis的配置`

```xml
    <!--BEGIN: Redis配置 -->
    <bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <!--最大空闲数-->
        <property name="maxIdle" value="50"/>
        <!--最大连接数-->
        <property name="maxTotal" value="100"/>
        <!--最大等待毫秒数-->
        <property name="maxWaitMillis" value="20000"/>
    </bean>

    <bean id="JedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <property name="hostName" value="localhost"/>
        <property name="password" value="admin"/>
        <property name="port" value="6379"/>
        <property name="poolConfig" ref="poolConfig"/>
    </bean>
    <!--序列器-->
    <bean id="stringRedisSerializer"
          class="org.springframework.data.redis.serializer.StringRedisSerializer"/>

    <!--序列器-->
    <bean id="JdkSerializationRedisSerializer"
          class="org.springframework.data.redis.serializer.JdkSerializationRedisSerializer"/>

    <bean class="org.springframework.data.redis.core.RedisTemplate">
        <property name="connectionFactory" ref="JedisConnectionFactory"/>
        <property name="keySerializer" ref="stringRedisSerializer"/>
        <property name="valueSerializer" ref="stringRedisSerializer"/>
    </bean>
    <!--END: Redis配置-->
```



关于版本问题，spring版本和spring-data-redis和jedis版本搭配如果不匹配，会导致无法初始化，出现

`NoSuchMethodError: redis.clients.jedis.JedisShardInfo.setTimeout(I)V`的报错 ，实验到一种可以的搭配是：spring4.2.4 data-redis版本是1.4.2.RELEASE jedis版本为2.6.0，尽可能拉低jedis版本号。







## HASH

|                  命令                  |                   说明                   |         备注         |
| :------------------------------------: | :--------------------------------------: | :------------------: |
|     hdel key field1[field2.......]     |         删除hash结构中某些键值对         |   可以进行批量删除   |
|           hexists key field            |      判断是否存在field字段的键值对       | 存在返回1,否则返回0  |
|              hgetall key               |         获取所有hash结构的键值对         |      返回键和值      |
|      hincrby key field increment       |  给指定的hash结构的某个字段加上一个整数  | 要求该字段也是整数型 |
|    hincrbyfloat key field increment    | 给指定的hash结构的某个字段加上一个浮点数 |   要求该字段是数字   |
|               hkeys key                |            返回hash中所有的键            |                      |
|                hlen key                |          返回hash中键值对的数量          |                      |
|     hmget key field1[field2......]     |    返回hash中指定的键的值,可以是多个     |       依次返回       |
|          hset key field value          |          在hash结构中设置键值对          |       单个设值       |
| hmset key field1 value1[field2 value2] |         hash结构中设置多个键值对         |                      |
|         hsetnx key field value         |    当hash结构中不存在该键时,才设置值     |                      |
|               hvals key                |          获取hash结构中的所有值          |                      |
|                                        |                                          |                      |







