# 关于SpringCloud和Feign的一些坑

最近在学习springCloud,其中在涉及Feign远程调用服务的过程中遇到了一个bug,过程如下:

- 一开始的报错为超时报错:

  > FeignClient java.net.SocketTimeoutException: Read timed out executing POST http:*//*******
  >
  > 

超时错误,百度了一下,设置一下ribbon:

```yaml
ribbon:
  ReadTimeout: 120000
  ConnectTimeout: 30000
```

- 再次进行尝试,发现报错依旧,怀疑是原本服务端(生产者)出问题,遂回去检查进行一次单元测试,发现没有问题,但同时发现该警告:

  > com.mysql.cj.core.exceptions.ConnectionIsClosedException: No operations allowed after connection closed.

于是发现可能是远端调用服务方的时候 数据库出现问题,查询时间过长导致超时,进行测试后发现是驱动问题:

即spring.datasource.driver-class-name 应该使用com.mysql.jdbc.Driver而非带cj的版本.更换之后bug解决,具体原因为止 待谷歌查一下.



超时的原因估摸着和HikariCP关系大一点,应该把Hikari的maxlifetime（注意计算随机误差）设置成比数据库连接超时更短的时间