springmvc全注解开发

spring的注解分为3个部分,一一做介绍把:

## WebAppInitializer

首先需要编写一个WebAppInitializer,继承自`AbstractAnnotationConfigDispatcherServletInitializer`

```java
public class WebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
	//导入与Spring容器相关的配置
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{RootConfig.class};
    }
	//导入Springmvc相关的配置
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }
	//注册DispatcherServlet的路径
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
	//注册一系列的Filter
    @Override
    protected Filter[] getServletFilters() {
        return new Filter[]{new CrossOriginesourceSharingFilter(),new CharacterEncodingFilter("UTF-8")};
    }
}
```

## RootConfig

这个配置类主要是与Spring容器本身相关

```java
@Configuration
@ComponentScan("com.gdou.mps.service")
public class RootConfig {
}

```



