# windows安装Mysql 8.0.25

## 1、下载压缩包

下载地址：

## 2、解压到指定目录

```
D:\environment\mysql 8.0.25
```

## 3、使用管理员模式打开命令提示符（CMD）

```
D:\environment\mysql 8.0.25\mysql-8.0.25-winx64\bin>
```

如果不使用管理员模式，后面 install 的时候会报错

```shell
Install/Remove of the service Denied
```

## 4、初始化 MySql 数据库

执行安装命令，并设置默认 root 密码为空，如下：

```
mysqld --initialize-insecure
```

初始化完成后，在 mysql 根目录会自动生成 data 文件夹。

## 5、为 Windos 系统安装 MySql 服务

执行命令：

```
mysqld install MySQL_8.0
```

## 6、启动服务后，需要设置 MySQL 的登录密码

启动 mySql 服务，使用命令登陆 mysql

```
mysql -u root -p
```

由于开始并没有设置密码，直接按 enter 进入即可。

使用命令：

```
use mysql;
```

```
alter user 'root'@'localhost' identified by '您的密码';
```

```
flush privileges;
```

注意：这点同5.7版本的安装方法。

退出后，使用新密码登陆，如果成功，则更改正确

```
mysql -u root -p
```

## 7、使用可视化工具连接 MySql

```
ALTER USER root@localhost IDENTIFIED WITH mysql_native_password BY '你的密码';
```

```
flush privileges;
```

## 8、对于 my.ini的配置

```
[mysqld]
# 设置3306端口
port=3306
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
# 关闭ssl
skip_ssl
# 配置时区
default-time_zone='+8:00'
# 配置相关参数
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8mb4
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8mb4
```

## 9、重启 MySql 服务