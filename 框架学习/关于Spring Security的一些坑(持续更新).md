# 关于Spring Security的一些坑(持续更新)

1.问题一

> 使用springboot，权限管理使用spring security，使用内存用户验证，但无响应报错：java.lang.IllegalArgumentException: There is no PasswordEncoder mapped for the id "null"



这个问题极其坑爹.

究其原因是因为我看的springboot的视频版本太老了导致的:

原先的代码:

```java
    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
        auth
                .inMemoryAuthentication()
                .withUser("admin").password("123456").roles("USER");
    }
```

如果单纯这样配置,必定报上面的错误。

原因在于Securuty5.0之后,强制性要求提供一个密码编码器才能通过验证,否则必报上面的空值异常



因此,多种解决方案

第一种,使用spring自带的:

此代码摘自[官网](<https://spring.io/guides/gs/securing-web/#initial>)

```java
    @Bean
    @Override
    public UserDetailsService userDetailsService() {
        UserDetails user =
             User.withDefaultPasswordEncoder()
                .username("user")
                .password("password")
                .roles("USER")
                .build();

        return new InMemoryUserDetailsManager(user);
    }
```

这种就是默认使用了校验器



第二种:自定义passwordEncoder

```java
//注意,这里将其标记为Component
@Component
public class MyPasswordEncoder implements PasswordEncoder {
    @Override
    public String encode(CharSequence charSequence) {
        return charSequence.toString();
    }

    @Override
    public boolean matches(CharSequence charSequence, String s) {
        return s.equals(charSequence.toString());
    }
}

```



然后config:

```java
//1.开启Security功能
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    //进行认证配置
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.
                authorizeRequests()
                .antMatchers("/user/login","/","/index.html").permitAll()
                .anyRequest().authenticated()
                .and()
                .formLogin();
                .and()
               .csrf().disable();
    }

    
    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
        //若前面写了Component,则可以采用这种方式
        auth
                .inMemoryAuthentication()
                .withUser("admin").password("123456").roles("USER");
        //没有将PasswordEncoder注册到容器中的话,需要这么写
//        auth.inMemoryAuthentication().
//                passwordEncoder(new MyPasswordEncoder()).
//                withUser("cxh").password("cxh").roles("USER");
    }

    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/webjars/**","/asserts/**");
    }
}

```

这样就可以完成登陆了