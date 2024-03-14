# Linux安装Nginx

## 1、下载nginx：

https://www.nginx.cn/nginx-download   下载最稳定版

![image-20201119155109832](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201119155109832.png)

## 2、**先安装Nginx依赖的包:**

nginx是C语言开发，建议在linux上运行，本教程使用Centos8作为安装环境。

### 2.1 n gcc

安装nginx需要先将官网下载的源码进行编译，编译依赖gcc环境，如果没有gcc环境，需要安装gcc

```
yum install gcc-c++
```

### 2.2 n PCRE

PCRE(Perl Compatible Regular Expressions)是一个Perl库，包括 perl 兼容的正则表达式库。nginx的http模块使用pcre来解析正则表达式，所以需要在linux上安装pcre库。

```
yum install -y pcre pcre-devel
```

注：pcre-devel是使用pcre开发的一个二次开发库。nginx也需要此库。

### 2.3 n zlib

zlib库提供了很多种压缩和解压缩的方式，nginx使用zlib对http包的内容进行gzip，所以需要在linux上安装zlib库。

```
yum install -y zlib zlib-devel
```

### 2.4 n openssl

OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序供测试或其它目的使用。

nginx不仅支持http协议，还支持https（即在ssl协议上传输http），所以需要在linux安装openssl库。

```
yum install -y openssl openssl-devel
```



## 3、安装Nginx

3.1 、把nginx的源码上传到linux系统

3.2、把压缩包解压缩。 

```
tar -zxvf nginx-1.8.1.tar.gz 
```

3.3、进入解压目录进行configure。

```
./configure
```

3.4、make && make install

遇见的错误

nginx 编译 make的时候报错 错误：this statement may fall through [-Werror=implicit-fallthrough=] 解决

![image-20201119160030340](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201119160030340.png)

注: 以下所有的 /usr/local/java/ 都换成自己的路径即可
按照以下步骤即可解决:(亲测)
进入 解压的地方

1. cd /usr/local/java/nginx-1.9.9/objs
2. vim Makefile

![image-20201119160357118](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201119160357118.png)

将把里面一个单词 -Werror 删除掉 然后保存一下。

3、进入目录:
cd /usr/local/java/nginx-1.9.9/src/os/unix/

4、vim ngx_user.c

![image-20201119160625124](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201119160625124.png)

将第二句话注释掉。

然后回到解压目录，执行make && make install命令

## 4、启动Nginx

安装成功后，在usr/local中，会多出一个nginx目录，进入sbin中，有启动脚本

![image-20201119161005765](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201119161005765.png)



## 5、访问

浏览器直接输入 ip + 80 即可访问

![image-20201119161558902](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201119161558902.png)

查看开发的端口号

```
firewall-cmd --list-all
```

设置开发的端口号

```
firewall-cmd -add-service=http -permanent
sudo firewall-cmd -add-port=80/tcp --permanent
```

重启防火墙

```
firewall-cmd --reload
```

