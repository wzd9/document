# ElasticSearch安装和使用

## ElasticSearch安装

### 安装之前需要安装node.js

1、官网：https://nodejs.org/en/download/

2、安装后，使用node -v，能查看版本说明安装成功。此处说明下：新版的Node.js已自带npm，安装Node.js时会一起安装，npm的作用就是对Node.js依赖的包进行管理，也可以理解为用来安装/卸载Node.js需要装的东西

3、环境配置

在目录中新建node_global和node-cache两个文件夹

说明：这里的环境配置主要配置的是npm安装的全局模块所在的路径，以及缓存cache的路径，之所以要配置，是因为以后在执行类似：npm install express [-g] （后面的可选参数-g，g代表global全局安装的意思）的安装语句时，会将安装的模块安装到【C:\Users\用户名\AppData\Roaming\npm】路径中，占C盘空间。

例如：我希望将全模块所在路径和缓存路径放在我node.js安装的文件夹中，则在我安装的文件夹【D:\environment\nodejs】下创建两个文件夹【node_global】及【node_cache】如下图：

![image-20201221163843107](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201221163843107.png)

创建完两个空文件夹之后，打开cmd命令窗口，输入

```
npm config set prefix "D:\environment\nodejs\node_global"
npm config set cache "D:\environment\nodejs\node_cache"
```

4、配置环境变量

进入环境变量对话框，在【系统变量】下新建【NODE_PATH】，输入【D:\environment\nodejs\node_global\node_modules】，在【用户变量】下的【Path】新增：【D:\environment\nodejs\node_global】

![image-20201221164137743](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201221164137743.png)

5、测试

配置完后，安装个module测试下，我们就安装最常用的express模块，打开cmd窗口，
输入如下命令进行模块的全局安装：

```
npm install express -g     # -g是全局安装的意思
```

![image-20201221164305258](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201221164305258.png)

安装cnpm，打开cmd,执行

```
npm install cnpm -g   # -g是全局安装的意思
```



### 下载ElasticSearch:

官网：https://www.elastic.co/cn/downloads/elasticsearch

解压后即可使用



### 下载ElasticSearch Head插件

1、官网：https://github.com/mobz/elasticsearch-head

2、启动

```
nmp install  #可以使用cnmp install,速度比较快
nmp run start
```

3、测试连接发现存在跨域问题：配置elasticsearch.yml文件

```
http.cors.enabled: true
http.cors.allow-origin: "*"
```

4、重启elasticsearch，再次连接

![image-20201221163215783](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201221163215783.png)

### Kibana的安装

1、官网：https://www.elastic.co/cn/downloads/kibana

2、解压后进入bin目录，打开kibana.bat

![image-20201221171240345](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201221171240345.png)



![image-20201221171403561](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201221171403561.png)

3、kibana汉化

修改kibana.yml文件

![image-20201221171747269](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201221171747269.png)

![image-20201221171640436](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201221171640436.png)

将en改为zn-CN.重新启动即可。

![image-20201221171822457](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201221171822457.png)



## ElasticSearch使用

### 关于索引的操作

#### 1、创建一个索引

```
PUT /索引名/类型名/文档id
{请求体}
```

![image-20201222144458920](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222144458920.png)

![image-20201222144618983](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222144618983.png)



#### 2、基本数据类型

- 字符串类型

  text、 keyword

- 数值类型

  long, integer, short, byte, double, float,half float, scaled float 

- 日期类型

  date

- 布尔值类型

  boolean

- 二进制类型

  binary

等等。

#### 3、指定字段类型

创建索引规则

![image-20201222145248502](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222145248502.png)

![image-20201222145322084](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222145322084.png)

获取规则，可以通过GET获取索引的信息。

![image-20201222145546459](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222145546459.png)

#### 4、查看默认的信息

![image-20201222145850066](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222145850066.png)

![image-20201222145919800](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222145919800.png)

如果自己的文档字段没有指定，那么elasticSearch就会默认配置字段类型。

通过 GET _cat/    可以获得elasticSearch当前的信息

![image-20201222150226819](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222150226819.png)



#### 5、修改索引

修改、提交还是使用PUT即可，然后覆盖

曾经的方法：

![image-20201222150706236](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222150706236.png)

现在的办法：

![image-20201222151012663](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222151012663.png)

#### 6、删除索引

通过DELETE命令实现删除，根据请求来判断是删除索引还是删除文档记录！

使用RESTFUL风格，是ES推荐使用的。

![image-20201222151148570](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222151148570.png)

### 关于文档的操作

#### 1、添加数据

```
PUT /wzd/user/1
{
  "name": "wzd",
  "age": "18",
  "desc": "最是人间留不住，朱颜辞镜花辞树",
  "tages": ["文档","中华"]
}
```

![image-20201222152431570](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222152431570.png)

#### 2、获取数据 GET

![image-20201222152736724](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222152736724.png)

#### 3、更新数据 PUT

![_](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222152912120.png)

#### 4、POST  _update,推荐使用这种更新方式

```
POST /wzd/user/3/_update
{
  "doc":{
    "name": "王五"
  }
}
```

![image-20201222153134949](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222153134949.png)

![image-20201222153156364](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222153156364.png)

#### 5、简单的搜索

```
GET /wzd/user/2
```

简单的条件查询

```
GET wzd/user/_search?q=name:wzd
```

![image-20201222153720429](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201222153720429.png)

![image-20201223151306665](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201223151306665.png)

#### 6、复杂的搜索 

select (排序、分页、高亮、模糊查询、精准查询)

![image-20201223151558520](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201223151558520.png)

![image-20201223152410218](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201223152410218.png)

```
查询特定字段
```

![image-20201223152223844](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201223152223844.png)



```
排序  ：sort
分页  ：from:从第几个开始
	   to: 返回多少条数据（当前页面的数据）
```

![image-20201223154905420](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201223154905420.png)

数据下标还是从0开始的。

``` 
布尔值查询
must: 所有条件都要符合，相对与and
should: 有一个条件符合就行，相当于or
```

![image-20201223155400891](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201223155400891.png)



![image-20201223155513938](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201223155513938.png)

```
过滤： filter  
gt : 大于
lt : 小于
gte: 大于等于
lte: 小于等于
```

```
精确查询
term :查询是直接通过倒排索引指定的词条进行精确的查找的
```

```
关于分词：
term: 直接查询精确的
match: 会使用分词器解析（先分析文档，然后在通过分析的文档进行查询）
```

两个类型：
text： 会被分词器解析。

keyword: 不会被分词器解析。

```
多个条件查询：
多个条件查询，使用空格隔开，只要满足其中一个结果可以被查询,这个时候通过分值基本的判断
```

```
高亮查询
```

![image-20201223160826243](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201223160826243.png)

![image-20201223161046194](C:\Users\wangrui\AppData\Roaming\Typora\typora-user-images\image-20201223161046194.png)