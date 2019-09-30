# Thymeleaf

该文章基于springboot环境

## 使用

springboot进行了自动配置,在其Autoconfiguration中,有下面代码:

```java
@ConfigurationProperties(prefix = "spring.thymeleaf")
public class ThymeleafProperties {

	private static final Charset DEFAULT_ENCODING = Charset.forName("UTF-8");

	private static final MimeType DEFAULT_CONTENT_TYPE = MimeType.valueOf("text/html");

	public static final String DEFAULT_PREFIX = "classpath:/templates/";

	public static final String DEFAULT_SUFFIX = ".html";
  	//
```

注明默认是将静态页面放在类路径下的/templates/中,thymeleaf就能自动渲染.

### 导入命名空间

使用前需要先导入命名空间

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

### 语法规则

- th:任意html原生属性	 替换原生属性的值

  - th:text	改变标签的text文本内容
  - th:utext       不转义特殊字符
  - th:insert 
  - th:each
  - th:if
  - th:object
  - th:attr

- 表达式:

  ```:british_indian_ocean_territory:
  Simple expressions:（表达式语法）
      Variable Expressions: ${...}：获取变量值；OGNL；
      		1）、获取对象的属性、调用方法
      		2）、使用内置的基本对象：
      			#ctx : the context object.当前上下文
      			#vars: the context variables.
                   #locale : the context locale. 地区信息
                   #request : (only in Web Contexts) the HttpServletRequest object. 
                   #response : (only in Web Contexts) the HttpServletResponse object.
                   #session : (only in Web Contexts) the HttpSession object.
                   #servletContext : (only in Web Contexts) the ServletContext object.
              3）、内置的一些工具对象：使用#调用内置对象
  #execInfo : information about the template being processed.
  #messages : methods for obtaining externalized messages inside variables expressions, in the same way as they would be obtained using #{…} syntax.
  #uris : methods for escaping parts of URLs/URIs
  #conversions : methods for executing the configured conversion service (if any).
  #dates : methods for java.util.Date objects: formatting, component extraction, etc.
  #calendars : analogous to #dates , but for java.util.Calendar objects.
  #numbers : methods for formatting numeric objects.
  #strings : methods for String objects: contains, startsWith, prepending/appending, etc.
  #objects : methods for objects in general.
  #bools : methods for boolean evaluation.
  #arrays : methods for arrays.
  #lists : methods for lists.
  #sets : methods for sets.
  #maps : methods for maps.
  #aggregates : methods for creating aggregates on arrays or collections.
  #ids : methods for dealing with id attributes that might be repeated (for example, as a result of an iteration).
  
  
  
  
  
  
  
  
      Selection Variable Expressions: *{...}：选择表达式：和${}在功能上是一样；
      	补充：配合 th:object="${session.user}：如果上文已经获取了user,下面可以使用*{}来获取其中的值
                 <div th:object="${session.user}">
                  <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
                  <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
                  <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
                  </div>
      
      
      
      
      Message Expressions: #{...}：获取国际化内容
      
      
      
      
      Link URL Expressions: @{...}：定义URL；
      避免像原生HTML的URL传参时需要拼串
      		@{/order/process(execId=${execId},execType='FAST')}
      		
      		
      		
      		
      Fragment Expressions: ~{...}：片段引用表达式
      		<div th:insert="~{commons :: main}">...</div>
      		有点像JSP的include
      		
  Literals（字面量）
        Text literals: 'one text' , 'Another one!' ,…
        Number literals: 0 , 34 , 3.0 , 12.3 ,…
        Boolean literals: true , false
        Null literal: null
        Literal tokens: one , sometext , main ,…
        
        
        
        
  Text operations:（文本操作）
      String concatenation: +
      Literal substitutions: |The name is ${name}|
      
      
      
  Arithmetic operations:（数学运算）
      Binary operators: + , - , * , / , %
      Minus sign (unary operator): -
      
      
      
  Boolean operations:（布尔运算）
      Binary operators: and , or
      Boolean negation (unary operator): ! , not
  Comparisons and equality:（比较运算）
      Comparators: > , < , >= , <= ( gt , lt , ge , le )
      Equality operators: == , != ( eq , ne )
  Conditional operators:条件运算（三元运算符）
      If-then: (if) ? (then)
      If-then-else: (if) ? (then) : (else)
      Default: (value) ?: (defaultvalue)
  Special tokens:
      No-Operation: _ 
  ```



### 举例

首先,写一个controller

```:british_indian_ocean_territory:
package com.gdou.springboot.controller;


import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.sql.Array;
import java.util.Arrays;
import java.util.Map;

@Controller
public class HelloController {

    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello";
    }

    @RequestMapping("/success")
    public String success(Map<String,Object> map){
            map.put("hello","<h1>hello</h1>");
            map.put("users", Arrays.asList("zhansan","lisi","wangwu"));
            return "success";
    }

}

```



然后,在默认的目录下面新建一个success.html

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>success</title>
</head>
<body>
<h1>成功</h1>
        <!--th:text设置文本内容-->
        <div th:text="${hello}">
            这是一段会被引擎覆盖的数据
        </div>
<hr/>
<div th:utext="${hello}">
</div>
<table>
    <tr th:each="user:${users}">
        <td th:text="${user}"></td>
    </tr>
</table>
<hr/>
<h4 th:each="user:${users}">
    [[${user}]]
</h4>
</body>
</html>
```



## 使用国际化文件

在thymeleaf中,使用国际化文件,使用的是#{}的格式进行引用,例如

```html
<label class="sr-only" th:text="#{login.username}">Username</label>
<input type="text" class="form-control" placeholder="Username" required="" autofocus="" th:placeholder="#{login.username}">
```

## 模板

thymeleaf有两种方法设置模板:id(这个ID属性不能是th:id)和th:fragment属性

```html
<div th:fragment="copy" id="copyid">
&copy; 2011 The Good Thymes Virtual Grocery
</div>
```

thymeleaf有三种方法抽取出模板

```html
<body>
...
<div th:insert="footer :: copy"></div>
<div th:replace="footer :: copy"></div>
<div th:include="footer :: copy"></div>
<div th:include="footer :: copyid"></div>
</body>
```

效果如下:

```html
<body>
...
<div>
<footer>
&copy; 2011 The Good Thymes Virtual Grocery
</footer>
</div>
<footer>
&copy; 2011 The Good Thymes Virtual Grocery
</footer>
<div>
&copy; 2011 The Good Thymes Virtual Grocery
</div>
</body>
```

### 模板还可以传入变量值

