## 如何在Springboot引入AOP

很简单

- pom.xml导入starter,具体可以参照sb的官网或者是idea创建的时候勾选上AOP模块

- application配置文件进行配置,这里以yaml为例子

  ```yaml
  spring:  
    aop:
      auto: true
  #    默认不使用CGLIB的时候,将proxy-target-class设置为false
      proxy-target-class: false
  ```

## 实现日志切面

几个注解:

`@Aspect`来注解一个java类是切面类

`@Pointcut`来定义一个切入点,可以是一个规则表达式,也可以是一个注解

`@Before`在切入点开始处切入内容

`@After`在切入点结尾处切入内容

`@AfterReturning`在切入点return内容之后切入内容(例如对返回值进行一些加工处理)

`@Around`在切入点前后切入内容.并控制自己何时执行切入点自身的内容

`@AfterThrowing`用来处理切入内容部分抛出异常后的处理逻辑



## 具体内容

```java
@Aspect
@Component
public class WebLogAspect {
  //日志
private Logger logger = Logger.getLogger(getClass());
    
//声明一个切点
//controller..*表示包下任意类
//*(..)任意方法,任意长度参数
@Pointcut("execution(public * com.gdou.springboot.controller..*.*(..))")
public void webLog(){}

//设置在方法执行前
@Before("webLog()")
public void doBefore(JoinPoint joinPoint) throws Throwable {
 //使用RequestContextHolder
// 接收到请求， 记录请求内容
ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
HttpServletRequest request = attributes.getRequest();
// 记录下请求内容
logger.info("URL : " + request.getRequestURL().toString());
logger.info("HTTP_METHOD : " + request.getMethod());
logger.info("IP : " + request.getRemoteAddr());
logger.info("CLASS_METHOD : " + joinPoint.getSignature().getDeclaringTypeNa
me() + "." + joinPoint.getSignature().getName());
logger.info("ARGS : " + Arrays.toString(joinPoint.getArgs()));
} @
AfterReturning(returning = "ret", pointcut = "webLog()")
public void doAfterReturning(Object ret) throws Throwable {
// 处理完请求， 返回内容
logger.info("RESPONSE : " + ret);
}
}
```

