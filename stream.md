## Stream流的用法

在工作中，常常因为需要处理DO->DTO->VO或者是各类对象集合转化的需求，使用Stream能够高效简洁的处理这些需求。

本文对Stream流的介绍仅仅在用法和技巧，并不会对原理进行过多的介绍。



Stream流的玩法有三步：

- 创建Stream流
- 中间操作
- 终点操作



### 创建Stream流

创建stream流有很多种方式，常见的有：

```java
//集合类的stream()方法。例如：
 List<Clazz> clazzes = new ArrayList<>();
 clazzes.stream()
//Arrays的stream方法，Arrays.stream(Object[])
Arrays.stream(new int[]{1,2,3})
//使用流的静态方法，比如Stream.of(Object[])
Stream.of("a","b","c","b")
   
```

常见的业务场景基本上这两种就足够了





### 中间操作

常见的中间操作有以下几个

- distnct

  distnct用于保证流里面的元素都是唯一的，他们是基于Object.equals方法来进行检查的

  ```java
  List l = Stream.of("a","b","c","b").distinct().collect(Collectors.toList());
  ```

- filter

  filter用户返回满足断言的数据，

  ```java
  List l = IntStream.range(1,100).filter( i -> i % 2 == 1)
   .collect(Collectors.toList());
  ```

  

- map

  这个是用的最多的，map将流的元素映射为其他另外的值，新的值类型可以和原来的不同

  ```java
  //这里举一个比较复杂的例子，但是可以说明有多好用
  
  //这是一个HashMap的List列表，从mybatis中获取到的，一个HashMap代表了一个值，如果按照普通的方法一个个去遍历List会很麻烦，但是使用stream能做到简洁高效
  List<HashMap> idList = userReExperimentMapper.getAllCountGroupByCourseId();
  
  //将其中的ID和ID对应的value值提取出来，并将其存入缓存
  List<Integer> existList = idList.stream().map((hashMap -> {
              Integer experiment_id = (Integer) hashMap.get("experiment_id");
              Long num = (Long) hashMap.get("num");
    					//存入缓存操作
              String bizCommitNumKey = RedisConstant.getBizCommitNumKey(experiment_id);
              stringRedisTemplate.opsForValue().set(bizCommitNumKey, String.valueOf(num));
              //将ID返回出去，可以改成自己需要的类型和格式
    					return Integer.valueOf(experiment_id);
          })).collect(Collectors.toList());
  ```

- limit

  limit方法指定数量的元素的流。

  ```java
  List l = IntStream.range(1,100).limit(5).collect(Collectors.toList());
  ```

- peek

  peek方法会使用一个Consumer消费流中的元素，按人话讲就是你可以在peek方法里面对你的元素进行操作，比如说是打印，比如说上文的进行缓存之类的，但是请注意，peek是一个中间操作，而如果你对peek的流不进行任何操作，它是不会执行的。

  ```java
  //例如这段代码不会执行
  Stream.of("one", "two", "three", "four").peek(e -> System.out.println(e));
  //但是这个就会	
  Stream.of("one", "two", "three", "four").peek(e -> System.out.println(e)).collect(Collectors.toList());
  ```

- sorted

  方法如其名，就是排序，sorted()将流中的元素按照自然排序方式进行排序，如果元素没有实现Comparable，则终点操作执行时会抛出java.lang.ClassCastException异常。sorted(Comparator comparator)可以指定排序的方式。

  ```java
  Arrays.stream(new int[]{1,2,3,0}).sorted()
  ```



### 终点操作

- count

  返回流中的元素的数量。

- collect

  这个是最实用的，上面基本上都用到它了。

  ```java
  //例如在上面，我使用map对参数进行了转换
  List<Integer> existList = idList.stream().map((hashMap -> {
             	//此处省略100字
              //将ID返回出去，可以改成自己需要的类型和格式
    					return Integer.valueOf(experiment_id);
    //此时我需要将这个Integer流变成集合给我输出出来，可以使用collect(Collectors.toList());
          })).collect(Collectors.toList());
  ```

  Collectors类的其他方法就交给你们自己去挖掘了。

- forEach

  这个也是算很常见的一个终点方法，假如你并不想消费你的peek()给出的流，那你应该选择这个forEach，你可以对流中的元素进行消费调用。

- close

  这个方法如其名，就是关闭流，不进行任何操作。终点方法



Stream还有其他很多的用法，在JDK已经出到12的年代，不要再觉得jdk8的新特性是什么新特性了，都是基础中的基础。只有努力去掌握这些基础，才能避免时代被淘汰。