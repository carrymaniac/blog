Linux命令行

```
shell中的特殊变量：
变量名
含义
$0
shell或shell脚本的名字
$*
以一对双引号给出参数列表
$@
将各个参数分别加双引号返回
$#
参数的个数
$_
代表上一个命令的最后一个参数
$$
代表所在命令的PID
$!
代表最后执行的后台命令的PID
$?
代表上一个命令执行后的退出状态  echo $? 如果返回值是0，就是执行成功；如果是返回值是0以外的值，就是失败。
```



## 压缩和解压

	>- -s 还原文件的顺序和备份文件内的存放顺序相同。
	>- -t 列出备份文件的内容。
	>- -v 显示指令执行过程。
	>- -f 指定压缩文件
	>- -x 从备份文件中还原文件。
	>- -c: 建立压缩档案
	>- -z：有gzip属性的
	>-  -j：有bz2属性的

- 将/etc文件夹中的文件归档到文件etc.tar（并不会进行压缩）：

```
tar -cvf /mydata/etc.tar /etc
复制代码
```

- 用gzip压缩文件夹/etc中的文件到文件etc.tar.gz：

```
tar -zcvf /mydata/etc.tar.gz /etc
复制代码
```

- 用bzip2压缩文件夹/etc到文件/etc.tar.bz2：

```
tar -jcvf /mydata/etc.tar.bz2 /etc
```

- 分页查看压缩包中内容（gzip）：

```shell
tar -ztvf /mydata/etc.tar.gz |more -c -10
```

- 解压文件到当前目录（gzip）：

```shell
tar -zxvf /mydata/etc.tar.gz
```