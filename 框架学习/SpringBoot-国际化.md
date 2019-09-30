# SpringBoot国际化



## 确定国际化文件

> 在resources目录下,建立i18n目录,存放国际化信息,同时进行编写

同时,在application.properties文件中,设定好`spring.messages.basename`表明使用的国际化文件

## 使用国际化文件

在thymeleaf中,使用国际化文件,使用的是#{}的格式进行引用,例如

```html
<label class="sr-only" th:text="#{login.username}">Username</label>
<input type="text" class="form-control" placeholder="Username" required="" autofocus="" th:placeholder="#{login.username}">

```

