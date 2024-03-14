

# CentOs8防火墙操作命令

### 查看防火墙某个端口是否开放

```shell
firewall-cmd --query-port=6379/tcp
```

### 开放防火墙端口

```shell
firewall-cmd --zone=public --add-port=6379/tcp --permanent
```

### 关闭端口

```shell
firewall-cmd --zone=public --remove-port=6379/tcp --permanent
```

### 重新载入配置，让开放或关闭的端口配置生效 

```
firewall-cmd --reload
```

### 查看防火墙状态

```shell
systemctl status firewalld
```

### 关闭防火墙

```shell
systemctl stop firewalld
```

### 打开防火墙

```shell
systemctl start firewalld
```

### 重启防火墙

```shell
systemctl restart firewalld
```

### 开放一段端口

```
firewall-cmd --zone=public --add-port=40000-45000/tcp --permanent
```

### 查看开放的端口列表

```
firewall-cmd --zone=public --list-ports
```

​              