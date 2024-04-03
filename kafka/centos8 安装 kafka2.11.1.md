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

## 7、设置多代理集群

​	到目前为止，我们一直在使用单个代理，这并不好玩。对 Kafka来说，单个代理只是一个大小为一的集群，除了启动更多的代理实例外，没有什么变化。 为了深入了解它，让我们把集群扩展到三个节点（仍然在本地机器上）。

​	首先，为每个代理创建一个配置文件 (在Windows上使用`copy` 命令来代替)：

```shell
cp config/server.properties config/server-1.properties
cp config/server.properties config/server-2.properties
```

注意：不要在 config 目录下执行上述指令，不然会提示错误：

```shell
cp: 无法获取'config/server.properties' 的文件状态(stat): 没有那个文件或目录
```

现在编辑这些新文件并设置如下属性：

```shell
config/server-1.properties:
    broker.id=1
    listeners=PLAINTEXT://:9093
    log.dir=/tmp/kafka-logs-1
 
config/server-2.properties:
    broker.id=2
    listeners=PLAINTEXT://:9094
    log.dir=/tmp/kafka-logs-2
```

​	broker.id 属性是集群中每个节点的名称，这一名称是唯一且永久的。我们必须重写端口和日志目录，因为我们在同一台机器上运行这些，我们不希望所有的代理尝试在同一个端口注册，或者覆盖彼此的数据。

我们已经建立Zookeeper和一个单节点了，现在我们只需要启动两个新的节点：

```shell
> bin/kafka-server-start.sh config/server-1.properties &
...
> bin/kafka-server-start.sh config/server-2.properties &
...
```

现在创建一个副本为3的新topic：

```shell
> bin/kafka-topics.sh --create --zookeeper 192.168.43.116:2181 --replication-factor 3 --partitions 1 --topic my-replication-topic
```

Good，现在我们有一个集群，但是我们怎么才能知道那些代理在做什么呢？运行"describe topics"命令来查看：

```shell
bin/kafka-topics.sh --describe --zookeeper 192.168.43.116:2181 --topic my-replication-topic
Topic:my-replication-topic   PartitionCount:1    ReplicationFactor:3 Configs:
	Topic: my-replication-topic  Partition: 0    Leader: 1   Replicas: 1,2,0 Isr: 1,2,0
```

以下是对输出信息的解释。第一行给出了所有分区的摘要，下面的每行都给出了一个分区的信息。因为我们只有一个分区，所以只有一行。

```
“leader”是负责给定分区所有读写操作的节点。每个节点都是随机选择的部分分区的领导者。
“replicas”是复制分区日志的节点列表，不管这些节点是leader还是仅仅活着。
“isr”是一组“同步”replicas，是replicas列表的子集，它活着并被指到leader。
```

请注意，在示例中，节点1是该主题中唯一分区的领导者。

我们可以在已创建的原始主题上运行相同的命令来查看它的位置：

```shell
bin/kafka-topics.sh --describe --zookeeper 192.168.43.116:2181 --topic test
Topic:test      PartitionCount:1        ReplicationFactor:1     Configs:
        Topic: test     Partition: 0    Leader: 0       Replicas: 0     Isr: 0
```

这没什么大不了，原来的主题没有副本且在服务器0上。我们创建集群时，这是唯一的服务器。

让我们发表一些信息给我们的新topic：

```shell
> bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my-replication-topic
...
my test message 1
my test message 2
^C
```

现在我们来消费这些消息：

```shell
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic my-replication-topic
my test message 1
my test message 2
^C
```

让我们来测试一下容错性。 Broker 1 现在是 leader，让我们来杀了它：

```shell
> ps aux | grep server-1.properties
7564 ttys002  0:15.91 ``/System/Library/Frameworks/JavaVM``.framework``/Versions/1``.8``/Home/bin/java``...
> kill -9 7564
```

在 Windows 上用:

```shell
> wmic process where "caption = 'java.exe' and commandline like '%server-1.properties%'" get processid
ProcessId
6016
> taskkill /pid 6016 /f
```

领导权已经切换到一个从属节点，而且节点1也不在同步副本集中了：

```
> bin/kafka-topics.sh --describe --zookeeper 192.168.43.116:2181 --topic my-replication-topic
Topic:my-replication-topic   PartitionCount:1    ReplicationFactor:3 Configs:
    Topic: my-replication-topic  Partition: 0    Leader: 2   Replicas: 1,2,0 Isr: 2,0
```

不过，即便原先写入消息的leader已经不在，这些消息仍可用于消费：

```
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic my-replicated-topic
...
my test message 1
my test message 2
^C
```

## 8、使用Kafka Connect来导入/导出数据

​	从控制台读出数据并将其写回是十分方便操作的，但你可能需要使用其他来源的数据或将数据从Kafka导出到其他系统。针对这些系统， 你可以使用Kafka Connect来导入或导出数据，而不是写自定义的集成代码。

Kafka Connect是Kafka的一个工具，它可以将数据导入和导出到Kafka。它是一种可扩展工具，通过运行*connectors（连接器）*， 使用自定义逻辑来实现与外部系统的交互。 在本文中，我们将看到如何使用简单的connectors来运行Kafka Connect，这些connectors 将文件中的数据导入到Kafka topic中，并从中导出数据到一个文件。

首先，我们将创建一些种子数据来进行测试：