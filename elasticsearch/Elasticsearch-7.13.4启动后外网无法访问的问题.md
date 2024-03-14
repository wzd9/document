# Elasticsearch-7.13.4启动后外网无法访问的问题

## 一、外网访问问题

默认情况下，是不支持外网访问，如果你的Elasticsearch安装在其他机器上，你从外网去访问的时候，访问不到，不通。
那么需要修改配置。
进入文件夹：

```
/home/work/elasticsearch-7.13.4/config
```

修改`elasticsearch.yml`文件，添加

```
network.host: 0.0.0.0
```

![image-20210724105704414](C:\Users\wzd-pc\AppData\Roaming\Typora\typora-user-images\image-20210724105704414.png)

## 二、启动后会报错：

![image-20210724105745565](C:\Users\wzd-pc\AppData\Roaming\Typora\typora-user-images\image-20210724105745565.png)

## 三、解决方案

1、针对问题一：

![image-20210724105843465](C:\Users\wzd-pc\AppData\Roaming\Typora\typora-user-images\image-20210724105843465.png)

在文件中添加一行配置

![image-20210724105949822](C:\Users\wzd-pc\AppData\Roaming\Typora\typora-user-images\image-20210724105949822.png)

```
vm.max_map_count=655360
```

查看是否生效

```
sysctl -p
```

![image-20210724110050233](C:\Users\wzd-pc\AppData\Roaming\Typora\typora-user-images\image-20210724110050233.png)

2、针对问题二

放开`elasticsearch.yml`文件中的以下内容

![image-20210724110154024](C:\Users\wzd-pc\AppData\Roaming\Typora\typora-user-images\image-20210724110154024.png)



参考：

https://blog.csdn.net/wd2014610/article/details/89532638



## 四：启动问题

```
[2021-07-26T11:48:07,054][INFO ][o.e.b.BootstrapChecks    ] [localhost.localdomain] bound or publishing to a non-loopback address, enforcing bootstrap checks
ERROR: [1] bootstrap checks failed. You must address the points described in the following [1] lines before starting Elasticsearch.
bootstrap check failure [1] of [1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
ERROR: Elasticsearch did not exit normally - check the logs at /opt/elasticsearch-7.13.4/logs/elasticsearch.log
[2021-07-26T11:48:07,095][INFO ][o.e.n.Node               ] [localhost.localdomain] stopping ...
[2021-07-26T11:48:07,126][INFO ][o.e.n.Node               ] [localhost.localdomain] stopped
[2021-07-26T11:48:07,127][INFO ][o.e.n.Node               ] [localhost.localdomain] closing ...
[2021-07-26T11:48:07,143][INFO ][o.e.n.Node               ] [localhost.localdomain] closed
[2021-07-26T11:48:07,145][INFO ][o.e.x.m.p.NativeController] [localhost.localdomain] Native controller process has stopped - no new native processes can be started

```

问题翻译过来就是：elasticsearch用户拥有的可创建文件描述的权限太低，至少需要65536；

出现这种问题，解决办法如下：

\#切换到root用户修改

```
vim /etc/security/limits.conf
```

在后面追加内容

```
*** hard nofile 65536
*** soft nofile 65536

***  是启动ES的用户
```

```
ulimit -n 65535
```

使用 ulimit -Hn 查看当前值，果然是65535，

也就是说每次更新环境变量的时候limits.conf的hard nofile 131072设置被覆盖掉了
这就好办了，vi /etc/profile 将 ulimit -n 65535 行注释掉，退出重新进入当前用户，再使用 ulimit -Hn 查看当前值，已经是131072了，设置成功！

```
 ulimit -Hn 65536
```

### 确保非root账号打开文件数量也增大

1、打开/etc/security/limits.conf，在里面添加如下内容

```
* soft nofile 65536
* hard nofile 65536
```

其中*表示所有用户 nofile表示最大文件句柄数,表示能够打开的最大文件数目

2.编辑/etc/pam.d/common-session，添加如下内容

```
vi /etc/pam.d/common-session
session required pam_limits.so
```

3.编辑/etc/profile，添加如下内容

```
ulimit -SHn 65536
```

然后重新启动机器,再利用ulimit -n查看文件句柄数,发现文件句柄数变为65536

参考：

https://blog.csdn.net/jiahao1186/article/details/90235771

