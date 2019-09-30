[TOC]

# Spring

## IOC

### IOC以及容器



### 依赖注入

- 构造器注入

  在xml配置文件中,`<bean>`声明中定义一个`<constructor-arg index="0" value="AAAA">`的方法调用构造方法创造对象

- setter注入

  最主流方式,在`<bean>`声明中加入`<property name="id" value="XXXXX">`,这样Spring会通过反射调用没有参数的构造方法生成对象

- 接口注入

  <TODO>

### 装配Bean



####XML方式装配

- 简易值装配:`<property name="id" value="XXXXX">`

- 集合的装配

  ```xml
  List
  <list>
  	<value>1</value>
      <value>2</value>
      <value>3</value>
      <value>4</value>
  </list>
  Map
  <map>
      <entry key = "key1" value="value1"/>
      <entry key = "key2" value="value2"/>
      <entry key = "key3" value="value3"/>    
  </map>
  Set
  <set>
  	<value>value-set1</value>
      <value>value-set2</value>
      <value>value-set3</value>
      <value>value-set4</value>
  </set>
  Array
  <array>
      <value>value-array1</value>
      <value>value-array2</value>
      <value>value-array3</value>
      <value>value-array4</value>
  </array>
  ```

- 命名空间装配

####注解装配

##### 组件扫描

​	通过定义资源的方式,让SpringIOC容器扫描对应的包,从而将Bean装配进来

- @Component装配Bean

  ```java
  @Component(value = "role")
  public class Role{
      @Value("1")
  	private Long id ;
      @Value("role_name 1")
      private String roleName;
      private String note ;
  }
  ```

  带上注解@Component代表IOC容器会将这个类扫描为Bean实例,其中value属性代表这个bean在IOC容器里的ID

- 做完以上配置之后,需要创建一个JAVAConfig来告诉IOC容器去哪里扫描对象

  ```java
  @ComponentScan
  public class PojoConfig{
  
  }
  ```


##### 自动装配

​	通过注解定义,使得依赖关系可以通过注解完成

- @Autowired
- @Primary @Qualifier
- @Bean

#### 混合使用(XML与注解)



### 其他

####profile

####加载properties

####条件化装配

#### Bean作用域

 

## AOP

### 面向切面



## Spring与数据库

## Spring事务



# SpringMVC

## SpringMVC初始化

### SpringMVC原理流程

### SpringMVC基本配置

## SpringMVC组件

### 参数接收

### 重定向

### 保存参数

### 拦截器

### 表单验证

### 视图与视图解析

### 文件上传





## SpringMVC高级应用

### SpringMVC的数据转换和格式化

### Spring异常处理

### SpringMVC国际化

