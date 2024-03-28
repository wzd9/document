# centos8 安装 kafka2.11.1

## 1、下载 kafka2.11.1

[下载地址](https://www.apache.org/dyn/closer.cgi?path=/kafka/1.0.0/kafka_2.11-1.0.0.tgz)

## 2、使用如下命令解压缩

```shell
tar -zxf kafka_2.11-1.0.0.tgz
cd kafka_2.11-1.0.0
```

## 3、启动服务器

​	进入 kafka_2.11-1.0.0 的根目录（**注意：不是 bin 目录**），执行如下命令：

```shell
bin/zookeeper-server-start.sh config/zookeeper.properties &
```

该命令可以启动 kafka中自带的 zookeeper 配置。

关闭上一个命令启动的 zookeeper 进程（不关闭的话，后续启动 kafka可能会提示地址被占用，如果关闭后，kafka启动后提示拒绝连接，则重新启动zookeeper）。使用如下命令启动 kafka:

```shell
bin/kafka-server-start.sh config/server.properties &
```

## 4、创建一个 topic

​	让我们创建一个名为“test”的topic，它有一个分区和一个副本：

```shell
bin/kafka-topics.sh --create --zookeeper 192.168.43.116:2181 --replication-factor 1 --partitions 1 --topic test
```

现在我们可以运行list（列表）命令来查看这个topic：

```shell
 bin/kafka-topics.sh --list --zookeeper 192.168.43.116:2181
 > test
```

## 5、发送一些消息

​	Kafka自带一个命令行客户端，它从文件或标准输入中获取输入，并将其作为message（消息）发送到Kafka集群。默认情况下，每行将作为单独的message发送。

运行 producer，然后在控制台输入一些消息以发送到服务器。

```shell
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
>this is message
>this is another message
```

## 6、启动一个 consumer

​	Kafka 还有一个命令行consumer（消费者），将消息转储到标准输出。

```shell
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
This is a message
This is another message
```

​	如果您将上述命令在不同的终端中运行，那么现在就可以将消息输入到生产者终端中，并将它们在消费终端中显示出来。

所有的命令行工具都有其他选项；运行不带任何参数的命令将显示更加详细的使用信息。