# centos8安装jdk

## 1、下载jdk8

[下载地址](https://www.oracle.com/cn/java/technologies/downloads/)

**注意：一定要下载通系统相匹配的jdk版本.我的系统是64位的，jdk也需要下载64位的，否则最后使用 java -verison会出现linux下安装jdk显示没有此文件或文件夹的问题。**

```shell
-bash: /usr/local/java/environment/jdk/jdk8/jdk1.8.0_401/bin/java: 没有那个文件或目录
```

## 2、centos8解压缩

```shell
mkdir -p /usr/local/java/environment/jdk/jdk8/
tar -zxvf jdk-8u401-linux-x64.tar.gz
```

## 3、环境变量配置(“=”前后请勿添加空格，否则加载时会出错)

修改/etc目录下的profile文件

```shell
vim /etc/profile
```

按 "i" 编辑文件内容，添加完内容后按“Esc”停止编辑，按“:wq”保存并退出。

在profile文件末尾添加如下内容：

```shell
#set java environment
export JAVA_HOME=/usr/local/java/environment/jdk/jdk8/jdk1.8.0_401
export JRE_HOME=$JAVA_HOME/jre
export CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
```

其中 JAVA_HOME需要根据实际安装路径和JDK版本进行修改。

修改完成后，执行如下命令使修改生效：

```
source /etc/profile
```

执行 java-versioin 查看jdk是否安装成功

```shell
[root@192 java]# java -version
java version "1.8.0_401"
Java(TM) SE Runtime Environment (build 1.8.0_401-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.401-b10, mixed mode)
[root@192 java]# 
```

