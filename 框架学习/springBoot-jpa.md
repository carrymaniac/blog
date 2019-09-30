springboot+JPA

- idea勾选mysql JPA模块

## 编写entity类

```java
package com.gdou.survey.demo.entity;

import javax.persistence.*;
import java.io.Serializable;
import java.util.Date;

//用户类
@Entity//注明是一个实体类,注意 这个导入的是javax.persistence.*的
@Table(name="tb_user")//和数据库的表进行关联
public class User implements Serializable {
    //用户ID
    @Id//注明是一个主键
    @GeneratedValue(strategy = GenerationType.AUTO)//注明主键自增
    private Integer userId;
    //提交日期
    @Column(name = "creat_time")//和数据库表中的字段进行关联
    private Date creatTime;
    //用户名字
    @Column(name = "user_name")
    private String userName;
    //用户问题一答案
    @Column(name = "user_q1")
    private String userQ1;
    //用户问题二答案
    @Column(name = "user_q2")
    private String userQ2;
    //用户问题三答案
    @Column(name = "user_q3")
    private String userQ3;
    //用户问题四答案
    @Column(name = "user_q4")
    private String userQ4;
    //用户问题五答案
    @Column(name = "user_q5")
    private String userQ5;
    //用户问题六答案
    @Column(name = "user_q6")
    private String userQ6;
    //用户问题六答案
    @Column(name = "user_q7")
    private String userQ7;
    //用户手机
    @Column(name = "user_tel")
    private String userTel;
    //用户表单隶属业务员
    @Column(name = "admin_info")
    private String adminInfo;

    public User() {
    }

    @Override
    public String toString() {
        return "User{" +
                "userId=" + userId +
                ", creatTime=" + creatTime +
                ", userName='" + userName + '\'' +
                ", userQ1='" + userQ1 + '\'' +
                ", userQ2='" + userQ2 + '\'' +
                ", userQ3='" + userQ3 + '\'' +
                ", userQ4='" + userQ4 + '\'' +
                ", userQ5='" + userQ5 + '\'' +
                ", userQ6='" + userQ6 + '\'' +
                ", userQ7='" + userQ7 + '\'' +
                ", userTel='" + userTel + '\'' +
                ", adminInfo='" + adminInfo + '\'' +
                '}';
    }

    public Integer getUserId() {
        return userId;
    }

    public void setUserId(Integer userId) {
        this.userId = userId;
    }

    public Date getCreatTime() {
        return creatTime;
    }

    public void setCreatTime(Date creatTime) {
        this.creatTime = creatTime;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public String getUserQ1() {
        return userQ1;
    }

    public void setUserQ1(String userQ1) {
        this.userQ1 = userQ1;
    }

    public String getUserQ2() {
        return userQ2;
    }

    public void setUserQ2(String userQ2) {
        this.userQ2 = userQ2;
    }

    public String getUserQ3() {
        return userQ3;
    }

    public void setUserQ3(String userQ3) {
        this.userQ3 = userQ3;
    }

    public String getUserQ4() {
        return userQ4;
    }

    public void setUserQ4(String userQ4) {
        this.userQ4 = userQ4;
    }

    public String getUserQ5() {
        return userQ5;
    }

    public void setUserQ5(String userQ5) {
        this.userQ5 = userQ5;
    }

    public String getUserQ6() {
        return userQ6;
    }

    public void setUserQ6(String userQ6) {
        this.userQ6 = userQ6;
    }

    public String getUserQ7() {
        return userQ7;
    }

    public void setUserQ7(String userQ7) {
        this.userQ7 = userQ7;
    }

    public String getUserTel() {
        return userTel;
    }

    public void setUserTel(String userTel) {
        this.userTel = userTel;
    }

    public String getAdminInfo() {
        return adminInfo;
    }

    public void setAdminInfo(String adminInfo) {
        this.adminInfo = adminInfo;
    }

    public User(Integer userId, Date creatTime, String userName, String userQ1, String userQ2, String userQ3, String userQ4, String userQ5, String userQ6, String userQ7, String userTel, String adminInfo) {
        this.userId = userId;
        this.creatTime = creatTime;
        this.userName = userName;
        this.userQ1 = userQ1;
        this.userQ2 = userQ2;
        this.userQ3 = userQ3;
        this.userQ4 = userQ4;
        this.userQ5 = userQ5;
        this.userQ6 = userQ6;
        this.userQ7 = userQ7;
        this.userTel = userTel;
        this.adminInfo = adminInfo;
    }
}

```

## 编写Repository

```java
package com.gdou.survey.demo.repository;

import com.gdou.survey.demo.entity.User;
import org.springframework.data.repository.PagingAndSortingRepository;

public interface UserRepository extends PagingAndSortingRepository<User, Integer> {

}
```

编写一个UserRepository类,继承PagingAndSortingRepository(具有分页和排序功能的Repository)或CrudRepository(具有普通CRUD功能的Repository),其中使用了泛型:

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package org.springframework.data.repository;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
//其中T表示该Repository操作的entity,ID表示主键类型
@NoRepositoryBean
public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID> {
    Iterable<T> findAll(Sort var1);

    Page<T> findAll(Pageable var1);
}

```

## 编写Controller

```java
package com.gdou.survey.demo.controller;

import com.gdou.survey.demo.entity.User;
import com.gdou.survey.demo.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.view.json.MappingJackson2JsonView;

import java.util.Date;
import java.util.Optional;

@RestController
@RequestMapping(path = "/demo")
public class UserController {
    //自动注入即可
    @Autowired
    private UserRepository userRepository;

    @GetMapping(value = "/user/{id}")
    public User getUser(@PathVariable("id")Integer id ){
        //调用方法获得Optional
        Optional<User> user = userRepository.findById(id);
        if(user.isPresent()){
            return user.get();
        }
        return null;
    }
    @RequestMapping(value = "/save")
    public User addNewUser(@RequestBody User user){
        user.setCreatTime(new Date());
        userRepository.save(user);
        return user;
    }
}
```

## 相关配置

配置spring.datasource的数据,使用yml配置

```java
spring:
  datasource:
    username: root
    password: admin
    driver-class-name: com.mysql.cj.jdbc.Driver
    url:  jdbc:mysql://localhost:3306/surveysystem?useSSL=false&characterEncoding=UTF-8&serverTimezone=UTC
  jpa:
    show-sql: true #配置JSP显示操作SQL语句

```

此处配置了多一个时区的配置,是因为笔者跑起来之后发现报错..只能加上这个配置避免报错



## 关于@Query注解

JPA中支持@Query注解查询适用于所查询的数据无法通过关键字查询得到结果的查询。这种查询可以摆脱像关键字查询那样的约束，将查询直接在相应的接口方法中声明，结构更为清晰，这是Spring Data的特有实现.

- 索引参数与命名参数

  - 索引参数如下所示，索引值从1开始，查询中**"?X"**个数需要与方法定义的参数个数相一致，并且顺序也要一致。　　　　　

    ```java
    @Query("SELECT p FROM Person p WHERE p.lastName = ?1 AND p.email = ?2")
    List<Person> testQueryAnnotationParams1(String lastName, String email);
    ```

    ```java
    // 为@Query注解传递参数的方式1：命名参数
    @Query("SELECT p FROM Person p WHERE p.lastName = :lastName AND p.email = :email")
    List<Person> testQueryAnnotationParams2(@Param("email") String email, @Param("lastName") String lastName);
    ```

- 含有LIKE关键字的查询

  - 可以在占位符上添加"%",这样在查询方法中就不用添加"%"

    ```java
    // like查询 Spring Date 允许在占位符上添加%
     @Query("SELECT p FROM Person p WHERE p.lastName LIKE %?1% OR p.email LIKE %?2%")
     List<Person> testQueryAnnotationLikeParam(String lastName, String email);
    
    @Test
    public void testAnnoationParams3() {
    List<Person> persons = personRepsitory.testQueryAnnotationLikeParam("A", "A@126.com");
     System.out.println(persons);
    }
    ```

- 使用原生SQL查询

  ```java
  　　　**
       * 设置nativeQuery=true 即可以使用原生的SQL进行查询
       * @return
       */
      @Query(value = "SELECT count(id) FROM jpa_persons", nativeQuery = true)
      long getTotalCount();
  ```


其中需要注意的几个点是:

- 如果采用的是JPA的query注解的话,sql语句中的表,字段应该写成entity类中的字段,而不是数据库的字段,这样的话JPA才会映射为数据库字段
- 相同的,如果使用的是nativeQuery() =true即采用原生SQL的话,记得将表和字段写成数据库表对应的字段,这一点很容易搞错
- JPA中的参数注入即(:param),注入的参数自动带上' ',这样避免了sql注入

## 关于更新部分字段

在Spring Data JPA 中，新增和更新操作都是用save()的方式进行，JPA是通过什么方法来知道我们是要进行insert还是update呢？ 经过测试，JPA对程序调用的save()方法判断是updata或者insert操作的依据是看实体对象的主键是否被赋值。 JPA首先会通过主键去查询数据库中是否已经有该ID，如果未查到，那么就执行insert方法，相反如果查到就会执行update方法。 

因此在更新部分字段上, JPA只能判断出是执行insert还是update，它不能判断出我们是否更新部分字段。所以没有被我们赋值的字段都会被覆盖为NULL。 由此，通过实体对象进行更新时不可取的。 JPA的更新字段的方法有两种： 

1. 通过设置主键进行save()保存。 使用save()方法更新字段一定要通过Repository获取实体对象，在此对象上进行更新操作。
2.  通过注解@Query实现复杂的sql语句。 在执行update或者delete方法时，必须加上注解@Modifying 和 @Transactional。

这里我采用的是第二种方法,直接来看代码:

```java
    @Modifying
    @Query(value = "UPDATE Admin set adminToken = :adminToken where id = :id")
    int updateAdminToken(@Param("adminToken") String adminToken ,@Param("id")Long id);
```

这里有几个点

1. update操作需要加上@Modifying注解,在@Query注解中编写JPQL实现DELETE和UPDATE操作的时候必须加上@modifying注解，以通知Spring Data 这是一个DELETE或UPDATE操作。

2. UPDATE或者DELETE操作需要使用事务，此时需要 定义Service层，在Service层的方法上添加事务操作。而笔者是使用test来进行JUNIT4测试的,而springboot的默认配置中,test类进行数据库更新和删除操作会进行回滚操作,因此需要加上一下注释:

   ```java
   @Transactional//采用事务
   @RunWith(SpringRunner.class)
   @SpringBootTest
   @Rollback(value = false)//默认springboot的事务控制对数据库操作进行了回滚,如果要测试可以加上注解Rollback设置为false
   public class DemoApplicationTests {
   ```


