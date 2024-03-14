# Centos8安装redis6.0.0

先去官网下载redis安装包。通过xftp将redis-6.0.6.tar.gz传到虚拟机上，通过命令解压。

$ wget http://download.redis.io/releases/redis-6.0.6.tar.gz

$ tar -zxvf redis-6.0.6.tar.gz -C /home/work

$ cd redis-6.0.6

$ make

执行make编译的时候，会c产生各种错误：

1：需要查检查一下gcc版本, Redis6要求 gcc 版本至少是 5.3,如果少于5.3就将gcc进行升级。

```xshell
gcc-v
```

![image-20210719195120875](C:\Users\wzd-pc\AppData\Roaming\Typora\typora-user-images\image-20210719195120875.png)

2、当出现如下错误时：

![image-20210719194826982](C:\Users\wzd-pc\AppData\Roaming\Typora\typora-user-images\image-20210719194826982.png)

通过

```
make MALLOC=libc 
```

执行即可。

原因请看此文：[redis编译报致命错误：jemalloc/jemalloc.h：没有那个文件或目录_honchou56的专栏-CSDN博客](https://blog.csdn.net/honchou56/article/details/53994708)。