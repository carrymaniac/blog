关于springboot2.0的升级问题



# springboot 升级到2.0的变动

### JPA的差别

JPA中的findOne需要改成新的为findById()

在测试是否有值可以进行返回时，可以使用findById().orElse(null)

其中使用`ifPresent(e -> e.addXXX)`来进行操作，此时则为如果存在则进行xxx

其中orElse中表示 当get为空的时候，返回的结果为河，这里直接返回null即可

### YML配置文件的中的区别

配置文件中不能使用驼峰命名，应该使用`-`来区别

Context-path发生了改动







