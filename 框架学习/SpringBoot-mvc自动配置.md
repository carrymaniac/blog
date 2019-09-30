# 关于springmvc在springboot的自动配置原理

### 自动配置

关于自动配置,可以参考一下官方给出的说明:

> The auto-configuration adds the following features on top of Spring’s defaults:
>
> 1. Inclusion of ContentNegotiatingViewResolver and BeanNameViewResolver 
>    beans.
> 2. Support for serving static resources, including support for WebJars 
>    (see below).
> 3. Automatic registration of Converter, GenericConverter, Formatter 
>    beans.
> 4. Support for HttpMessageConverters (see below).
> 5. Automatic registration of MessageCodesResolver (see below).
> 6. Static index.html support.
> 7. Custom Favicon support (see below).
> 8. Automatic use of a ConfigurableWebBindingInitializer bean (see below).

大概意思是:

>1. 引入ContentNegotiatingViewResolver和BeanNameViewResolver beans。
>2. 对静态资源的支持，包括对WebJars的支持。
>3. 自动注册Converter，GenericConverter，Formatter beans。
>4. 对HttpMessageConverters的支持。
>5. 自动注册MessageCodeResolver。
>6. 对静态index.html的支持。
>7. 对自定义Favicon的支持。
>8. 字段使用 ConfigurableWebBindingInitializer bean

1. 自动配置了ViewResolver视图解析器
    	1. ContentNegotiatingViewResolver:负责组合所有的视图解析器,其中不仅仅会配置自带的视图解析器,也会将我们自己配置的放在容器中的视图解析器加载进来
    	2. 因此,如果需要给自己的容器添加一个视图解析器,编写完后注册到Spring容器中,这样就可以自动的将其组合进来

2. `addResourceHandlers`对静态资源文件、webjar等文件进行了配置

3. `getIndexHtml`对静态首页进行了配置

4. Favicon 对图标进行了配置

5. 自动注册了一系列的`Converter`，`GenericConverter`，`Formatter`等beans

- Converter：转换器，对参数类型和后台的bean类型进行转换的组件

- Formatter：格式化器，例如对2019-3-8转换为Date数据类型

  ```
  @Bean
  @ConditionalOnProperty(prefix = "spring.mvc", name = "date-format")
  public Formatter<Date> dateFormatter() {
      return new DateFormatter(this.mvcProperties.getDateFormat());
  }
  ```

  关于这个类，我发现好像在Spring5.0之后 已经被删除了？并没有在WebMvcAutoConfiguration中找到这项目，待考证？

- Support for HttpMessageConverters (see below). 

  - HttpMessageConverter：SpringMVC用来转换Http请求和响应的；User---Json；
  - HttpMessageConverters 是从容器中确定；获取所有的HttpMessageConverter；
    自己给容器中添加HttpMessageConverter，只需要将自己的组件注册容器中
    （@Bean,@Component）

- Automatic registration of MessageCodesResolver (see below).定义错误代码生成规则

- Automatic use of a ConfigurableWebBindingInitializer bean (see below).


### 原理

springBoot对springmvc进行自动配置类为：(WebMvcAutoConfiguration)

先来看看其注解：

```java
@Configuration//配置类
@ConditionalOnWebApplication(type = Type.SERVLET)//指明是web环境
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })//当前类路径下存在这些类时，才能继续执行
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)//beanFactory中不存在WebMvcConfigurationSupport类型的bean时，才能继续执行，这也是为什么如果配置了@EnableWebMvc即可实现全面接管
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)//加载的优先级为Ordered.HIGHEST_PRECEDENCE + 10 –> Integer.MIN_VALUE + 10
@AutoConfigureAfter({ DispatcherServletAutoConfiguration.class,
		TaskExecutionAutoConfiguration.class, ValidationAutoConfiguration.class })
//在DispatcherServletAutoConfiguration, ValidationAutoConfiguration 加载后进行装配。
public class WebMvcAutoConfiguration {
```

再来看看其中的内部类：

- WebMvcAutoConfigurationAdapter

- EnableWebMvcConfiguration

- ResourceChainCustomizerConfiguration

其中，WebMvcAutoConfigurationAdapter声明为内部类是为了是为了确保当该类不在类路径下时不会被读取到.

WebMvcAutoConfiguration中的内部类WebMvcAutoConfigurationAdapter，其注解为

```java
	@Configuration
	@Import(EnableWebMvcConfiguration.class)
	@EnableConfigurationProperties({ WebMvcProperties.class, ResourceProperties.class })
	@Order(0)
	public static class WebMvcAutoConfigurationAdapter
```

其中，Import(EnableWebMvcConfiguration.class)，而这个EnableWebMvcConfiguration中

```java
@Configuration
	public static class EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration {

		private final WebMvcProperties mvcProperties;

		private final ListableBeanFactory beanFactory;

		private final WebMvcRegistrations mvcRegistrations;

其父类为：

@Configuration
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {

	private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();

	@Autowired(required = false)
	public void setConfigurers(List<WebMvcConfigurer> configurers) {
		if (!CollectionUtils.isEmpty(configurers)) {
			this.configurers.addWebMvcConfigurers(configurers);
		}
	}
    
    //其中的WebMvcConfigurerComposite configurers中，有很多方法，其中一个参考实现；将所有的WebMvcConfigurer相关配置都来一起调用；
    @Override
    // public void addViewControllers(ViewControllerRegistry registry) {
    // for (WebMvcConfigurer delegate : this.delegates) {
    // delegate.addViewControllers(registry);
    // }


```

setConfigurers中将所有的WebMvcConfigurer进行导入，这样的话也将我们的配置类进行导入，这样就实现了Springmvc和我们自己的配置类共同生效

### 全面接管MVC原理

@EnableWebMvc源码：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Import(DelegatingWebMvcConfiguration.class)
public @interface EnableWebMvc {
```

而DelegatingWebMvcConfiguration中

```java
@Configuration
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
```

此时再回头看看WebMvcAutoConfiguration的头部，有一个`@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)`,此时WebMvcAutoConfiguration自然就失效了

而WebMvcConfigurationSupport中，仅仅实现了SpringMVC最基本的功能，因此也就实现了全局控制



### 总结

1）、SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的（@Bean、@Component）如果有就用用户配置的，如果没有，才自动配置；如果有些组件可以有多个（如ViewResolver）将用户配置的和自己默认的组合起来；

2）、用户可以借助添加 WebMvcConfigurerAdapter类型的 @Configuration 类，而不需要注解@EnableWebMvc来拓展Springmvc的自定义配置。

3）、用户可以在配置类加上了@EnableWebMvc注解，实现全面控制SpringMvc配置。

4）、整个SpringBoot框架中（不仅仅是针对web模块），在SpringBoot中会有非常多的xxxConfigurer帮助我们进行扩展配置和有很多的’有很多的xxxCustomizer帮助我们进行定制配置’帮助我们进行定制配置。这些在后期会慢慢遇上。

