三大核心组件:

Subject:当前用户,在权限管理的应用程序里往往需要知道谁能够操作什么，谁拥有操作该程序的权利，shiro中则需要通过Subject来提供基础的当前用户信息，Subject 不仅仅代表某个用户与当前应用交互的任何东西都是Subject，如网络爬虫等。所有的Subject都要绑定到SecurityManager上，与Subject的交互实际上是被转换为与SecurityManager的交互。



SecurityManager：即所有Subject的管理者，这是Shiro框架的核心组件，可以把他看做是一个Shiro框架的全局管理组件，用于调度各种Shiro框架的服务。作用类似于SpringMVC中的DispatcherServlet，用于拦截所有请求并进行处理。



Realm：Realm是用户的信息认证器和用户的权限人证器，我们需要自己来实现Realm来自定义的管理我们自己系统内部的权限规则。SecurityManager要验证用户，需要从Realm中获取用户。可以把Realm看做是数据源。



### 导包

```xml
<dependencies>
    <!--shiro核心类库-->
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-core</artifactId>
        <version>1.2.3</version>
    </dependency>
    <!--日志工具包-->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.6.1</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>1.6.1</version>
    </dependency>
</dependencies>

```

log4j.properties文件:

```properties
log4j.rootLogger=INFO, stdout, 

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n

```



### 编写配置文件





shiro.ini文件

用户名 密码 角色







操作shiro

```java
Subject currentUser = SecurityUtils.getSubject

//测试使用session
//获取session
Session session = currentUser.getSession
session.setAttribute("someKey","aValue")
    
    
 //测试用户是否已经被验证
    if(!currentUser.isAuthenticated()){
        //把用户名密码进行封装为UsernamePasswordToken对象
        UsernamePasswordToken token = new UsernamePasswordToken("lonestarr","vespa");
             token.setRememberMe("true");
        try{
            //登陆
            currentUser.login(token);
        }catch(UnKnownAccountException uae){
            //未知用户
            XXXXXXXXXXXXXxxxx
        }
    }
```



