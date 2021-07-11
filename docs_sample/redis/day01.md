## 1. Redis 简介

###  Redis 的下载与安装

本课程所示，均基于Center OS7安装Redis。

```
0) 如果 wget 命令找不到
yum -y install wget
```

````
0)如果make  报错 安装 c 语言编译环境
yum install gcc-c++
````

(1)下载Redis

下载安装包：

```bash
wget http://download.redis.io/releases/redis-5.0.0.tar.gz
```

解压安装包：

```bash
tar -xvf redis-5.0.0.tar.gz
```

编译（在解压的目录中执行）：

```bash
make
```

安装（在解压的目录中执行）：

```bash
make install
```

（2）安装 Redis

redis-server，服务器启动命令 客户端启动命令

redis-cli，redis核心配置文件

redis.conf，RDB文件检查工具（快照持久化文件）

redis-check-dump，AOF文件修复工具

redis-check-aof



###  Redis服务器启动

####  Redis服务器启动

启动服务器——参数启动

```bash
redis-server [--port port]
```

范例

```bash
redis-server --port 6379
```

启动服务器——配置文件启动

```bash
redis-server config_file_name
```

范例

```bash
redis-server redis.conf
```

####  Redis客户端启动

启动客户端

```bash
redis-cli [-h host] [-p port]
```

范 例

```bash
redis-cli -h 61.129.65.248 -p 6384
```

注意：服务器启动指定端口使用的是--port，客户端启动指定端口使用的是-p。-的数量不同。

####  Redis基础环境设置约定

创建配置文件存储目录

```bash
mkdir conf
```

创建服务器文件存储目录（包含日志、数据、临时配置文件等）

```bash
mkdir data
```

创建快速访问链接

```bash
ln -s redis-5.0.0 redis
```



###  配置文件启动与常用配置

```sh
# 查看配置命令
cat redis.conf |grep -v '#' |grep -v '^$'
```

```sh
# 那些ip 能访问redis 
#bind 127.0.0.1
# 端口号
port 6379
# 超时时间
#timeout 0
# 是否后台启动
daemonize yes
# 日志文件
logfile "log_6379.log"
# 数据文件位置
dir /usr/local/redis/data
```



####  服务器端设定

设置服务器以守护进程的方式运行，开启后服务器控制台中将打印服务器运行信息（同日志内容相同）

```bash
daemonize yes|no
```

绑定主机地址

```bash
bind ip
```

设置服务器端口

```bash
port port
```

设置服务器文件保存地址

```bash
dir path
```

####   客户端配置

 服务器允许客户端连接最大数量，默认0，表示无限制。当客户端连接到达上限后，Redis会拒绝新的连接

```bash
maxclients count
```

客户端闲置等待最大时长，达到最大值后关闭对应连接。如需关闭该功能，设置为 0

```bash
timeout seconds
```

####   日志配置

设置服务器以指定日志记录级别

```bash
loglevel debug|verbose|notice|warning
```

日志记录文件名

```bash
logfile filename
```

注意：日志级别开发期设置为verbose即可，生产环境中配置为notice，简化日志输出量，降低写日志IO的频度。

###  Redis基本操作

####   命令行模式工具使用思考

功能性命令

帮助信息查阅

退出指令

清除屏幕信息

####   信息读写

设置 key，value 数据

```bash
set key value
```

范例

```bash
set name itheima
```

根据 key 查询对应的 value，如果不存在，返回空（nil）

```bash
get key
```

范例

```bash
get name
```

####   帮助信息

获取命令帮助文档

```bash
help [command]
```

范例

```bash
help set
```

获取组中所有命令信息名称

```bash
help [@group-name]
```

范例

```bash
help @string
```

退出命令行客户端模式

退出客户端

````bash
quit
exit
````

快捷键

```bash
Ctrl+C
```

#### 1.6.4  redis入门总结

到这里，Redis 入门的相关知识，我们就全部学习完了，再来回顾一下，这个部分我们主要讲解了哪些内容呢？

首先，我们对Redis进行了一个简单介绍，包括NoSQL的概念、Redis的概念等。

然后，我们介绍了Redis 的下载与安装。包括下载与安装、服务器与客户端启动、以及相关配置文件（3类）。

最后，我们介绍了Redis 的基本操作。包括数据读写、退出与帮助信息获取。

## 2. 数据类型

### 2.1  Redis 数据类型(5种常用)

基于以上数据特征我们进行分析，最终得出来我们的Redis中要设计5种 数据类型：

string、hash、list、set、sorted_set/zset（应用性较低）

### 2.2  string数据类型

在学习第一个数据类型之前，先给大家介绍一下，在随后这部分内容的学习过程中，我们每一种数据类型都分成三块来讲：首先是讲下它的基本操作，接下来讲一些它的扩展操作，最后我们会去做一个小的案例分析。

#### 2.2.1Redis 数据存储格式

在学习string这个数据形式之前，我们先要明白string到底是修饰什么的。我们知道redis 自身是一个 Map，其中所有的数据都是采用 key : value 的形式存储。

对于这种结构来说，我们用来存储数据一定是一个值前面对应一个名称。我们通过名称来访问后面的值。按照这种形势，我们可以对出来我们的存储格式。前面这一部分我们称为key。后面的一部分称为value，而我们的数据类型，他一定是修饰value的。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B42.png)

数据类型指的是存储的数据的类型，也就是 value 部分的类型，key 部分永远都是字符串。

#### 2.2.2  string 类型

（1）存储的数据：单个数据，最简单的数据存储类型，也是最常用的数据存储类型。

string，他就是存一个字符串儿，注意是value那一部分是一个字符串，它是redis中最基本、最简单的存储数据的格式。

（2）存储数据的格式：一个存储空间保存一个数据

每一个空间中只能保存一个字符串信息，这个信息里边如果是存的纯数字，他也能当数字使用，我们来看一下，这是我们的数据的存储空间。

（3）存储内容：通常使用字符串，如果字符串以整数的形式展示，可以作为数字操作使用.

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B42.png)

一个key对一个value，而这个itheima就是我们所说的string类型，当然它也可以是一个纯数字的格式。

#### 2.2.3  string 类型数据的基本操作

（1）基础指令

添加/修改数据添加/修改数据

```
set key value
```

获取数据

```
get key
```

删除数据

```
del key
```

判定性添加数据

```
setnx key value
```

添加/修改多个数据

```
mset key1 value1 key2 value2 …
```

获取多个数据

```
mget key1 key2 …
```

获取数据字符个数（字符串长度）

```
strlen key
```

追加信息到原始信息后部（如果原始信息存在就追加，否则新建）

```
append key value
```

（2）单数据操作与多数据操作的选择之惑

即set 与mset的关系。这对于这两个操作来说，没有什么你应该选哪个，而是他们自己的特征是什么，你要根据这个特征去比对你的业务，看看究竟适用于哪个。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B4.png)

假如说这是我们现在的服务器，他要向redis要数据的话，它会发出一条指令。那么当这条指令发过来的时候，比如说是这个set指令过来，那么它会把这个结果返回给你，这个时候我们要思考这里边一共经过了多长时间。

首先，发送set指令要时间，这是网络的一个时间，接下来redis要去运行这个指令要消耗时间，最终把这个结果返回给你又有一个时间，这个时间又是一个网络的时间，那我们可以理解为：一个指令发送的过程中需要消耗这样的时间.

但是如果说现在不是一条指令了，你要发3个set的话，还要多长时间呢？对应的发送时间要乘3了，因为这是三个单条指令,而运行的操作时间呢，它也要乘3了，但最终返回的也要发3次，所以这边也要乘3。

于是我们可以得到一个结论：单指令发3条它需要的时间，假定他们两个一样，是6个网络时间加3个处理时间，如果我们把它合成一个mset呢，我们想一想。

假如说用多指令发3个指令的话，其实只需要发一次就行了。这样我们可以得到一个结论，多指令发3个指令的话，其实它是两个网络时间加上3个redis的操作时间，为什么这写一个小加号呢，就是因为毕竟发的信息量变大了，所以网络时间有可能会变长。

那么通过这张图，你就可以得到一个结论，我们单指令和多指令他们的差别就在于你发送的次数是多还是少。当你影响的数据比较少的时候，你可以用单指令，也可以用多指令。但是一旦这个量大了，你就要选择多指令了，他的效率会高一些。

### 2.3  string 类型数据的扩展操作

#### 2.3.1  string 类型数据的扩展操作

下面我们来看一string的扩展操作，分成两大块：一块是对数字进行操作的，第二块是对我们的key的时间进行操作的。

设置数值数据增加指定范围的值

```bash
incr key
incrby key increment
incrbyfloat key increment
```

设置数值数据减少指定范围的值

```bash
decr key
decrby key increment
```

设置数据具有指定的生命周期

```bash
setex key seconds value
psetex key milliseconds value
```

#### 2.3.2  string 类型数据操作的注意事项

(1)数据操作不成功的反馈与数据正常操作之间的差异

表示运行结果是否成功

(integer) 0  → false                 失败

(integer) 1  → true                  成功

表示运行结果值

(integer) 3  → 3                        3个

(integer) 1  → 1                         1个

(2)数据未获取到时，对应的数据为（nil），等同于null

(3)数据最大存储量：512MB

(4)string在redis内部存储默认就是一个字符串，当遇到增减类操作incr，decr时会转成数值型进行计算

(5)按数值进行操作的数据，如果原始数据不能转成数值，或超越了redis 数值上限范围，将报错
9223372036854775807（java中Long型数据最大值，Long.MAX_VALUE）

(6)redis所有的操作都是原子性的，采用单线程处理所有业务，命令是一个一个执行的，因此无需考虑并发带来的数据影响.

### 2.4string应用场景与key命名约定

#### 2.4.1  应用场景

它的应用场景在于：主页高频访问信息显示控制，例如新浪微博大V主页显示粉丝数与微博数量。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B4.png)

我们来思考一下：这些信息是不是你进入大V的页面儿以后就要读取这写信息的啊，那这种信息一定要存储到我们的redis中，因为他的访问量太高了！那这种数据应该怎么存呢？我们来一块儿看一下方案！

#### 2.4.2  解决方案

（1）在redis中为大V用户设定用户信息，以用户主键和属性值作为key，后台设定定时刷新策略即可。

```markdown
eg:	user:id:3506728370:fans		→	12210947
eg:	user:id:3506728370:blogs	→	6164
eg:	user:id:3506728370:focuses	→	83
```

（2）也可以使用json格式保存数据

```markdown
eg:	user:id:3506728370    →	{“fans”：12210947，“blogs”：6164，“ focuses ”：83 }
```

（3） key 的设置约定

数据库中的热点数据key命名惯例

|       | **表名** | **主键名** | 主键值    | **字段名** |
| ----- | -------- | ---------- | --------- | ---------- |
| eg1： | order    | id         | 29437595  | name       |
| eg2： | equip    | id         | 390472345 | type       |
| eg3： | news     | id         | 202004150 | title      |

### 2.5  hash的基本操作

下面我们来学习第二个数据类型hash。

#### 2.5.1  数据存储的困惑

对象类数据的存储如果具有较频繁的更新需求操作会显得笨重！

在正式学习之前，我们先来看一个关于数据存储的困惑：

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B4.png)

比如说前面我们用以上形式存了数据，如果我们用单条去存的话，它存的条数会很多。但如果我们用json格式，它存一条数据就够了。问题来了，假如说现在粉丝数量发生变化了，你要把整个值都改了。但是用单条存的话就不存在这个问题，你只需要改其中一个就行了。这个时候我们就想，有没有一种新的存储结构，能帮我们解决这个问题呢。

我们一块儿来分析一下：

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B4.png)

如上图所示：单条的话是对应的数据在后面放着。仔细观察：我们看左边是不是长得都一模一样啊，都是对应的表名、ID等的一系列的东西。我们可以将右边红框中的这个区域给他封起来。

那如果要是这样的形式的话，如下图，我们把它一合并，并把右边的东西给他变成这个格式，这不就行了吗？

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B4.png)

这个图其实大家并不陌生，第一，你前面学过一个东西叫hashmap不就这格式吗？第二，redis自身不也是这格式吗？那是什么意思呢？注意，这就是我们要讲的第二种格式，hash。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B4.png)

在右边对应的值，我们就存具体的值，那左边儿这就是我们的key。问题来了，那中间的这一块叫什么呢？这个东西我们给他起个名儿，叫做field字段。那么右边儿整体这块儿空间我们就称为hash，也就是说hash是存了一个key value的存储空间。

#### 2.5.2  hash 类型

新的存储需求：对一系列存储的数据进行编组，方便管理，典型应用存储对象信息

需要的存储结构：一个存储空间保存多个键值对数据

hash类型：底层使用哈希表结构实现数据存储

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/set%E6%A8%A1%E5%9E%8B.png)

如上图所示，这种结构叫做hash，左边一个key，对右边一个存储空间。这里要明确一点，右边这块儿存储空间叫hash，也就是说hash是指的一个数据类型，他指的不是一个数据，是这里边的一堆数据，那么它底层呢，是用hash表的结构来实现的。

值得注意的是：

如果field数量较少，存储结构优化为类数组结构

如果field数量较多，存储结构使用HashMap结构

#### 2.5.3  hash 类型数据的基本操作

添加/修改数据

```bash
hset key field value
```

获取数据

```bash
hget key field
hgetall key
```

删除数据

```bash
hdel key field1 [field2]
```

设置field的值，如果该field存在则不做任何操作

```bash
hsetnx key field value
```

添加/修改多个数据

```bash
hmset key field1 value1 field2 value2 …
```

获取多个数据

```bash
hmget key field1 field2 …
```

获取哈希表中字段的数量

```bash
hlen key
```

获取哈希表中是否存在指定的字段

```bash
hexists key field
```

### 2.6  hash的拓展操作

在看完hash的基本操作后，我们再来看他的拓展操作，他的拓展操作相对比较简单：

#### 2.6.1  hash 类型数据扩展操作

获取哈希表中所有的字段名或字段值

```
hkeys key
hvals key
```

设置指定字段的数值数据增加指定范围的值

```
hincrby key field increment
hincrbyfloat key field increment
```

#### 2.6.2  hash类型数据操作的注意事项

(1)hash类型中value只能存储字符串，不允许存储其他数据类型，不存在嵌套现象。如果数据未获取到，对应的值为（nil）。

(2）每个 hash 可以存储 2^32 - 1 个键值对
hash类型十分贴近对象的数据存储形式，并且可以灵活添加删除对象属性。但hash设计初衷不是为了存储大量对象而设计 的，切记不可滥用，更不可以将hash作为对象列表使用。

(3)hgetall 操作可以获取全部属性，如果内部field过多，遍历整体数据效率就很会低，有可能成为数据访问瓶颈。

### 2.7  hash应用场景

#### 2.7.1  应用场景

双11活动日，销售手机充值卡的商家对移动、联通、电信的30元、50元、100元商品推出抢购活动，每种商品抢购上限1000  张。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/set%E6%A8%A1%E5%9E%8B.png)

也就是商家有了，商品有了，数量有了。最终我们的用户买东西就是在改变这个数量。那你说这个结构应该怎么存呢？对应的商家的ID作为key，然后这些充值卡的ID作为field，最后这些数量作为value。而我们所谓的操作是其实就是increa这个操作，只不过你传负值就行了。看一看对应的解决方案：

#### 2.7.2  解决方案

以商家id作为key

将参与抢购的商品id作为field

将参与抢购的商品数量作为对应的value

抢购时使用降值的方式控制产品数量

注意：实际业务中还有超卖等实际问题，这里不做讨论

### 2.8  list基本操作

前面我们存数据的时候呢，单个数据也能存，多个数据也能存，但是这里面有一个问题，我们存多个数据用hash的时候它是没有顺序的。我们平时操作，实际上数据很多情况下都是有顺序的，那有没有一种能够用来存储带有顺序的这种数据模型呢，list就专门来干这事儿。

#### 2.8.1  list 类型

数据存储需求：存储多个数据，并对数据进入存储空间的顺序进行区分

需要的存储结构：一个存储空间保存多个数据，且通过数据可以体现进入顺序

list类型：保存多个数据，底层使用双向链表存储结构实现

先来通过一张图，回忆一下顺序表、链表、双向链表。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/set%E6%A8%A1%E5%9E%8B.png)

list对应的存储结构是什么呢？里边存的这个东西是个列表，他有一个对应的名称。就是key存一个list的这样结构。对应的基本操作，你其实是可以想到的。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/set%E6%A8%A1%E5%9E%8B.png)

来看一下，因为它是双向的，所以他左边右边都能操作，它对应的操作结构两边都能进数据。这就是链表的一个存储结构。往外拿数据的时候怎么拿呢？通常是从一端拿，当然另一端也能拿。如果两端都能拿的话，这就是个双端队列，两边儿都能操作。如果只能从一端进一端出，这个模型咱们前面了解过，叫做栈。

#### 2.8.2 list 类型数据基本操作

最后看一下他的基本操作

添加/修改数据

```bash
lpush key value1 [value2] ……
rpush key value1 [value2] ……
```

获取数据

```bash
lrange key start stop
lindex key index
llen key
```

获取并移除数据

```bash
lpop key
rpop key
```

### 2.9  list扩展操作

#### 2.9.1  list 类型数据扩展操作

移除指定数据

```
lrem key count value
```

规定时间内获取并移除数据

```
blpop key1 [key2] timeout
brpop key1 [key2] timeout
brpoplpush source destination timeout
```

#### 2.9.2  list 类型数据操作注意事项

（1）list中保存的数据都是string类型的，数据总容量是有限的，最多2^32 - 1 个元素(4294967295)。

（2）list具有索引的概念，但是操作数据时通常以队列的形式进行入队出队操作，或以栈的形式进行入栈出栈操作

（3）获取全部数据操作结束索引设置为-1

（4）list可以对数据进行分页操作，通常第一页的信息来自于list，第2页及更多的信息通过数据库的形式加载



### 2.10 list 应用场景

#### 2.10.1  应用场景

企业运营过程中，系统将产生出大量的运营数据，如何保障多台服务器操作日志的统一顺序输出？

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/set%E6%A8%A1%E5%9E%8B.png)

假如现在你有多台服务器，每一台服务器都会产生它的日志，假设你是一个运维人员，你想看它的操作日志，你怎么看呢？打开A机器的日志看一看，打开B机器的日志再看一看吗？这样的话你会可能会疯掉的！因为左边看的有可能它的时间是11:01，右边11:02，然后再看左边11:03，它们本身是连续的，但是你在看的时候就分成四个文件了，这个时候你看起来就会很麻烦。能不能把他们合并呢？答案是可以的！怎么做呢？建立起redis服务器。当他们需要记日志的时候，记在哪儿,全部发给redis。等到你想看的时候，通过服务器访问redis获取日志。然后得到以后，就会得到一个完整的日志信息。那么这里面就可以获取到完整的日志了，依靠什么来实现呢？就依靠我们的list的模型的顺序来实现。进来一组数据就往里加，谁先进来谁先加进去，它是有一定的顺序的。

#### 2.10.2  解决方案

依赖list的数据具有顺序的特征对信息进行管理

使用队列模型解决多路信息汇总合并的问题

使用栈模型解决最新消息的问题

### 2.11  set 基本操作

#### 2.11.1 set类型

新的存储需求：存储大量的数据，在查询方面提供更高的效率

需要的存储结构：能够保存大量的数据，高效的内部存储机制，便于查询

set类型：与hash存储结构完全相同，仅存储键，不存储值（nil），并且值是不允许重复的

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/set%E6%A8%A1%E5%9E%8B.png)

通过这个名称，大家也基本上能够认识到和我们Java中的set完全一样。我们现在要存储大量的数据，并且要求提高它的查询效率。用list这种链表形式，它的查询效率是不高的，那怎么办呢？这时候我们就想，有没有高效的存储机制。其实前面咱讲Java的时候说过hash表的结构就非常的好，但是这里边我们已经有hash了，他做了这么一个设定，干嘛呢，他把hash的存储空间给改一下，右边你原来存数据改掉,全部存空，那你说数据放哪儿了？放到原来的filed的位置，也就在这里边存真正的值，那么这个模型就是我们的set 模型。

set类型：与hash存储结构完全相同，仅存储键，不存储值（nil），并且值是不允许重复的。

看一下它的整个结构：

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/list2.png)

#### 2.11.2 set类型数据的基本操作

添加数据

```bash
sadd key member1 [member2]
```

获取全部数据

```bash
smembers key
```

删除数据

```bash
srem key member1 [member2]
```

获取集合数据总量

```bash
scard key
```

判断集合中是否包含指定数据

```bash
 sismember key member
```

随机获取集合中指定数量的数据

```bash
srandmember key [count]
```

随机获取集中的某个数据并将该数据移除集合

```bash
spop key [count]
```

### 2.12  set 类型数据的扩展操作

#### 2.12.1  set 类型数据的扩展操作

求两个集合的交、并、差集

```
sinter key1 [key2 …]  
sunion key1 [key2 …]  
sdiff key1 [key2 …]
```

求两个集合的交、并、差集并存储到指定集合中

```
sinterstore destination key1 [key2 …]  
sunionstore destination key1 [key2 …]  
sdiffstore destination key1 [key2 …]
```

将指定数据从原始集合中移动到目标集合中

```
smove source destination member
```

通过下面一张图回忆一下交、并、差

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/list2.png)

#### 2.12.2  set 类型数据操作的注意事项

set 类型不允许数据重复，如果添加的数据在 set 中已经存在，将只保留一份。

set 虽然与hash的存储结构相同，但是无法启用hash中存储值的空间。

### 2.13  set应用场景

#### 2.13.1  set应用场景

（1）黑名单

资讯类信息类网站追求高访问量，但是由于其信息的价值，往往容易被不法分子利用，通过爬虫技术，  快速获取信息，个别特种行业网站信息通过爬虫获取分析后，可以转换成商业机密进行出售。例如第三方火 车票、机票、酒店刷票代购软件，电商刷评论、刷好评。

同时爬虫带来的伪流量也会给经营者带来错觉，产生错误的决策，有效避免网站被爬虫反复爬取成为每个网站都要考虑的基本问题。在基于技术层面区分出爬虫用户后，需要将此类用户进行有效的屏蔽，这就是黑名单的典型应用。

ps:不是说爬虫一定做摧毁性的工作，有些小型网站需要爬虫为其带来一些流量。

（2）白名单

对于安全性更高的应用访问，仅仅靠黑名单是不能解决安全问题的，此时需要设定可访问的用户群体， 依赖白名单做更为苛刻的访问验证。

#### 2.13.2  解决方案

基于经营战略设定问题用户发现、鉴别规则

周期性更新满足规则的用户黑名单，加入set集合

用户行为信息达到后与黑名单进行比对，确认行为去向

黑名单过滤IP地址：应用于开放游客访问权限的信息源

黑名单过滤设备信息：应用于限定访问设备的信息源

黑名单过滤用户：应用于基于访问权限的信息源

### 2.14  实践案例

#### 2.14.1业务场景

使用微信的过程中，当微信接收消息后，会默认将最近接收的消息置顶，当多个好友及关注的订阅号同时发 送消息时，该排序会不停的进行交替。同时还可以将重要的会话设置为置顶。一旦用户离线后，再次打开微信时，消息该按照什么样的顺序显示。

我们分析一下：

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/list2.png)

100这台手机代表你。而200、300、400这三台代表你好友的手机。在这里有一些东西需要交代一下，因为我们每个人的都会对自己的微信中的一些比较重要的人设置会话置顶，将他的那条对话放在最上面。我们假定这个人有两个会话置顶的好友，分别是400和500，而这里边就包含400.

下面呢，我们就来发这个消息，第一个发消息的是300，他发了个消息给100。发完以后，这个东西应该怎么存储呢？在这里面一定要分开，记录置顶的这些人的会话，对应的会话显示顺序和非置顶的一定要分两。

这里面我们创建两个模型，一个是普通的，一个是置顶的，而上面的这个置顶的用户呢，我们用set来存储，因为不重复。而下面这些因为有顺序，很容易想到用list去存储,不然你怎么表达顺序呢？

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/list2.png)

那当300发给消息给100以后，这个时候我们先判定你在置顶人群中吗？不在,那好，300的消息对应的顺序就应该放在普通的列表里边。而在这里边，我们把300加进去。第一个数据也就是现在300。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B42.png)

接下来400，发了个消息。判断一下，他是需要置顶的，所以400将进入list的置顶里边放着。当前还没有特殊的地方。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B42.png)

再来200发消息了，和刚才的判定方法一样，先看在不在置顶里，不在的话进普通，然后在普通里边把200加入就行了，OK，到这里目前还没有顺序变化。

接下来200又发消息过来，同一个人给你连发了两条，那这个时候200的消息到达以后，先判断是否在置顶范围，不在，接下来他要放在list普通中，这里你要注意一点，因为这里边已经有200，所以进来以后先干一件事儿，把200杀掉，没有200，然后再把200加进来，那你想一下，现在这个位置顺序是什么呢？就是新的都在右边，对不对？

还记得我们说list模型，如果是一个双端队列，它是可以两头进两头出。当然我们双端从一头进一头出，这就是栈模型，现在咱们运用的就是list模型中的栈模型。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B42.png)

现在300发消息，先判定他在不在，不在，用普通的队列，接下来按照刚才的操作，不管你里边原来有没有300，我先把300杀掉，没了，200自然就填到300的位置了，他现在是list里面唯一一个，然后让300进来，注意是从右侧进来的，那么现在300就是最新的。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/redis%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B42.png)

那么到这里呢，我们让100来读取消息。你觉得这个消息顺序应该是什么样的？首先置顶的400有一个，他跑在最上面，然后list普通如果出来的话，300是最新的消息，而200在他后面的。用这种形式，我们就可以做出来他的消息顺序来。

#### 2.14.2  解决方案

看一下最终的解决方案：

依赖list的数据具有顺序的特征对消息进行管理，将list结构作为栈使用

置顶与普通会话分别创建独立的list分别管理

当某个list中接收到用户消息后，将消息发送方的id从list的一侧加入list（此处设定左侧）

多个相同id发出的消息反复入栈会出现问题，在入栈之前无论是否具有当前id对应的消息，先删除对应id

推送消息时先推送置顶会话list，再推送普通会话list，推送完成的list清除所有数据
消息的数量，也就是微信用户对话数量采用计数器的思想另行记录，伴随list操作同步更新

#### 2.14.3  数据类型总结

总结一下，在整个数据类型的部分，我们主要介绍了哪些内容：

首先我们了解了一下数据类型，接下来针对着我们要学习的数据类型，进行逐一讲解了string、hash、list、set等，最后通过一个案例总结了一下前面的数据类型的使用场景。

## 3. 常用指令

在这部分中呢，我们家学习两个知识，第一个是key的常用指令，第二个是数据库的常用指令。和前面我们学数据类型做一下区分，前面你学的那些指令呢，都是针对某一个数据类型操作的，现在学的都是对所有的操作的，来看一下，我们在学习Key的操作的时候，我们先想一下的操作我们应该学哪些东西:

### 3.1  key 操作分析

#### 3.1.1  key应该设计哪些操作？

key是一个字符串，通过key获取redis中保存的数据

对于key自身状态的相关操作，例如：删除，判定存在，获取类型等

对于key有效性控制相关操作，例如：有效期设定，判定是否有效，有效状态的切换等

对于key快速查询操作，例如：按指定策略查询key

#### 3.1.2  key 基本操作

删除指定key

```bash
del key
```

获取key是否存在

```bash
exists key
```

获取key的类型

```bash
type key
```

3.1.3  拓展操作

排序

```bash
sort
```

改名

```bash
rename key newkey
renamenx key newkey
```

#### 3.1.3  key 扩展操作（时效性控制）

为指定key设置有效期

```bash
expire key seconds
pexpire key milliseconds
expireat key timestamp
pexpireat key milliseconds-timestamp
```

获取key的有效时间

```bash
ttl key
pttl key
```

切换key从时效性转换为永久性

```bash
persist key
```

#### 3.1.4  key 扩展操作（查询模式）

查询key

```bash
keys pattern
```

查询模式规则

*匹配任意数量的任意符号      ?	配合一个任意符号	[]	匹配一个指定符号

```bash
keys *  keys    查询所有
it*  keys       查询所有以it开头
*heima          查询所有以heima结尾
keys ??heima    查询所有前面两个字符任意，后面以heima结尾 查询所有以
keys user:?     user:开头，最后一个字符任意
keys u[st]er:1  查询所有以u开头，以er:1结尾，中间包含一个字母，s或t
```

### 3.2  数据库指令

#### 3.2.1  key 的重复问题

在这个地方我们来讲一下数据库的常用指令，在讲这个东西之前，我们先思考一个问题：

假如说你们十个人同时操作redis，会不会出现key名字命名冲突的问题。

一定会，为什么?因为你的key是由程序而定义的。你想写什么写什么，那在使用的过程中大家都在不停的加，早晚有一天他会冲突的。

redis在使用过程中，伴随着操作数据量的增加，会出现大量的数据以及对应的key。

那这个问题我们要不要解决？要！怎么解决呢？我们最好把数据进行一个分类，除了命名规范我们做统一以外，如果还能把它分开，这样是不是冲突的机率就会小一些了，这就是咱们下面要说的解决方案！

#### 3.2.2  解决方案

redis为每个服务提供有16个数据库，编号从0到15

每个数据库之间的数据相互独立

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/%E6%95%B0%E6%8D%AE%E5%BA%93.png)

在对应的数据库中划出一块区域，说他就是几，你就用几那块，同时，其他的这些都可以进行定义，一共是16个，这里边需要注意一点，他们这16个共用redis的内存。没有说谁大谁小，也就是说数字只是代表了一块儿区域，区域具体多大未知。这是数据库的一个分区的一个策略！

#### 3.2.3   数据库的基本操作

切换数据库

```
select index
```

其他操作

```
ping
```

#### 3.2.4  数据库扩展操作

数据移动

```
move key db
```

数据总量

```
dbsize
```

数据清除

```
flushdb  flushall
```

## 4. Jedis

在学习完redis后，我们现在就要用Java来连接redis了，也就是我们的这一章要学的Jedis了。在这个部分，我们主要讲解以下3个内容：

HelloWorld（Jedis版）

Jedis简易工具类开发

可视化客户端

### 4.1  Jedis简介

#### 4.1.1  编程语言与redis

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/%E5%8F%AF%E8%A7%86%E5%8C%96.png)

对于我们现在的数据来说，它是在我们的redis中，而最终我们是要做程序。那么程序就要和我们的redis进行连接。干什么事情呢？两件事：程序中有数据的时候，我们要把这些数据全部交给redis管理。同时，redis中的数据还能取出来，回到我们的应用程序中。那在这个过程中，在Java与redis之间打交道的这个东西就叫做Jedis.简单说，Jedis就是提供了Java与redis的连接服务的，里边有各种各样的API接口，你可以去调用它。

除了Jedis外，还有没有其他的这种连接服务呢？其实还有很多，了解一下：

Java语言连接redis服务 Jedis（SpringData、Redis 、 Lettuce）

其它语言：C 、C++ 、C# 、Erlang、Lua 、Objective-C 、Perl 、PHP 、Python 、Ruby 、Scala

#### 4.1.2  准备工作

(1)jar包导入

下载地址：https://mvnrepository.com/artifact/redis.clients/jedis

基于maven

```xml
<dependency>
<groupId>redis.clients</groupId>
<artifactId>jedis</artifactId>
<version>2.9.0</version>
</dependency>
```

(2)客户端连接redis

连接redis

```
Jedis jedis = new Jedis("localhost", 6379);
```

操作redis

```
jedis.set("name", "itheima");  jedis.get("name");
```

关闭redis连接

```
jedis.close();
```

API文档

http://xetorthio.github.io/jedis/

#### 4.1.3 代码实现

创建：com.itheima.JedisTest

```java
public class JedisTest {

    public static void main(String[] args) {
        //1.获取连接对象
        Jedis jedis = new Jedis("192.168.40.130",6379);
        //2.执行操作
        jedis.set("age","39");
        String hello = jedis.get("hello");
        System.out.println(hello);
        jedis.lpush("list1","a","b","c","d");
        List<String> list1 = jedis.lrange("list1", 0, -1);
        for (String s:list1 ) {
            System.out.println(s);
        }
        jedis.sadd("set1","abc","abc","def","poi","cba");
        Long len = jedis.scard("set1");
        System.out.println(len);
        //3.关闭连接
        jedis.close();
    }
}
```



### 4.2  Jedis简易工具类开发

前面我们做的程序还是有点儿小问题，就是我们的Jedis对象的管理是我们自己创建的，真实企业开发中是不可能让你去new一个的，那接下来咱们就要做一个工具类，简单来说，就是做一个创建Jedis的这样的一个工具。

#### 4.2.1  基于连接池获取连接

JedisPool：Jedis提供的连接池技术 

poolConfig:连接池配置对象 

host:redis服务地址

port:redis服务端口号



JedisPool的构造器如下：

```java
public JedisPool(GenericObjectPoolConfig poolConfig, String host, int port) {
this(poolConfig, host, port, 2000, (String)null, 0, (String)null);
}
```

#### 4.2.2  封装连接参数

创建jedis的配置文件：jedis.properties

```properties
jedis.host=192.168.40.130  
jedis.port=6379  
jedis.maxTotal=50  
jedis.maxIdle=10
```

#### 4.2.3  加载配置信息

 创建JedisUtils：com.itheima.util.JedisUtils，使用静态代码块初始化资源

```java
public class JedisUtils {
    private static int maxTotal;
    private static int maxIdel;
    private static String host;
    private static int port;
    private static JedisPoolConfig jpc;
    private static JedisPool jp;

    static {
        ResourceBundle bundle = ResourceBundle.getBundle("redis");
        maxTotal = Integer.parseInt(bundle.getString("redis.maxTotal"));
        maxIdel = Integer.parseInt(bundle.getString("redis.maxIdel"));
        host = bundle.getString("redis.host");
        port = Integer.parseInt(bundle.getString("redis.port"));
        //Jedis连接池配置
        jpc = new JedisPoolConfig();
        jpc.setMaxTotal(maxTotal);
        jpc.setMaxIdle(maxIdel);
        jp = new JedisPool(jpc,host,port);
    }

}
```

#### 4.2.4  获取连接

 对外访问接口，提供jedis连接对象，连接从连接池获取，在JedisUtils中添加一个获取jedis的方法：getJedis

```java
public static Jedis getJedis(){
	Jedis jedis = jedisPool.getResource();
	return jedis;
}
```



### 4.3  可视化客户端

4.3.1  Redis Desktop Manager

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/%E5%8F%AF%E8%A7%86%E5%8C%96.png)

## 5. 持久化

下面呢，进入到持久化的学习.这部分内容理解的东西多，操作的东西少。在这个部分，我们将讲解四个东西：

持久化简介

RDB

AOF

RDB与AOF区别

### 5.1  持久化简介

#### 5.1.1  场景-意外断电

不知道大家有没有遇见过，就是正工作的时候停电了，如果你用的是笔记本电脑还好，你有电池，但如果你用的是台式机呢，那恐怕就比较灾难了，假如你现在正在写一个比较重要的文档，如果你要使用的是word，这种办公自动化软件的话，他一旦遇到停电，其实你不用担心，因为它会给你生成一些其他的文件。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/%E6%8C%81%E4%B9%85%E5%8C%962.png)

其实他们都在做一件事儿，帮你自动恢复，有了这个文件，你前面的东西就不再丢了。那什么是自动恢复呢？你要先了解他的整个过程。

我们说自动恢复，其实基于的一个前提就是他提前把你的数据给存起来了。你平常操作的所有信息都是在内存中的，而我们真正的信息是保存在硬盘中的，内存中的信息断电以后就消失了，硬盘中的信息断电以后还可以保留下来！

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/%E6%8C%81%E4%B9%85%E5%8C%962.png)

我们将文件由内存中保存到硬盘中的这个过程，我们叫做数据保存，也就叫做持久化。但是把它保存下来不是你的目的，最终你还要把它再读取出来，它加载到内存中这个过程，我们叫做数据恢复，这就是我们所说的word为什么断电以后还能够给你保留文件，因为它执行了一个自动备份的过程，也就是通过自动的形式，把你的数据存储起来，那么有了这种形式以后，我们的数据就可以由内存到硬盘上实现保存。

#### 5.1.2  什么是持久化

(1)什么是持久化

利用永久性存储介质将数据进行保存，在特定的时间将保存的数据进行恢复的工作机制称为持久化 。

持久化用于防止数据的意外丢失，确保数据安全性。

(2)持久化过程保存什么？

我们知道一点，计算机中的数据全部都是二进制，如果现在我要你给我保存一组数据的话，你有什么样的方式呢，其实最简单的就是现在长什么样，我就记下来就行了，那么这种是记录纯粹的数据，也叫做快照存储，也就是它保存的是某一时刻的数据状态。

还有一种形式，它不记录你的数据，它记录你所有的操作过程，比如说大家用idea的时候，有没有遇到过写错了ctrl+z撤销，然后ctrl+y还能恢复，这个地方它也是在记录，但是记录的是你所有的操作过程，那我想问一下，操作过程，我都给你留下来了，你说数据还会丢吗？肯定不会丢，因为你所有的操作过程我都保存了。这种保存操作过程的存储，用专业术语来说可以说是日志，这是两种不同的保存数据的形式啊。

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/%E6%8C%81%E4%B9%85%E5%8C%962.png)



总结一下：

第一种：将当前数据状态进行保存，快照形式，存储数据结果，存储格式简单，关注点在数据。

第二种：将数据的操作过程进行保存，日志形式，存储操作过程，存储格式复杂，关注点在数据的操作过程。

### 5.2  RDB

#### 5.2.1  save指令

手动执行一次保存操作

```
save
```

**save指令相关配置**

设置本地数据库文件名，默认值为 dump.rdb，通常设置为dump-端口号.rdb

```
dbfilename filename
```

设置存储.rdb文件的路径，通常设置成存储空间较大的目录中，目录名称data

```
dir path
```

设置存储至本地数据库时是否压缩数据，默认yes，设置为no，节省 CPU 运行时间，但存储文件变大

```
rdbcompression yes|no
```

设置读写文件过程是否进行RDB格式校验，默认yes，设置为no，节约读写10%时间消耗，单存在数据损坏的风险

```
rdbchecksum yes|no
```

**save指令工作原理**

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/%E6%8C%81%E4%B9%85%E5%8C%962.png)

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/%E6%8C%81%E4%B9%85%E5%8C%962.png)

需要注意一个问题，来看一下，现在有四个客户端各自要执行一个指令，把这些指令发送到redis服务器后，他们执行有一个先后顺序问题，假定就是按照1234的顺序放过去的话，那会是什么样的？

记得redis是个单线程的工作模式，它会创建一个任务队列，所有的命令都会进到这个队列里边，在这儿排队执行，执行完一个消失一个，当所有的命令都执行完了，OK，结果达到了。

但是如果现在我们执行的时候save指令保存的数据量很大会是什么现象呢？

他会非常耗时，以至于影响到它在执行的时候，后面的指令都要等，所以说这种模式是不友好的，这是save指令对应的一个问题，当cpu执行的时候会阻塞redis服务器，直到他执行完毕，所以说我们不建议大家在线上环境用save指令。

#### 5.2.2  bgsave指令

之前我们讲到了当save指令的数据量过大时，单线程执行方式造成效率过低，那应该如何处理？

此时我们可以使用：**bgsave**指令，bg其实是background的意思，后台执行的意思

手动启动后台保存操作，但不是立即执行

```properties
bgsave
```

**bgsave指令相关配置**

后台存储过程中如果出现错误现象，是否停止保存操作，默认yes

```properties
stop-writes-on-bgsave-error yes|no
```

其 他

```properties
dbfilename filename  
dir path  
rdbcompression yes|no  
rdbchecksum yes|no
```

**bgsave指令工作原理**

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/%E6%8C%81%E4%B9%85%E5%8C%962.png)

当执行bgsave的时候，客户端发出bgsave指令给到redis服务器。注意，这个时候服务器马上回一个结果告诉客户端后台已经开始了，与此同时它会创建一个子进程，使用Linux的fork函数创建一个子进程，让这个子进程去执行save相关的操作，此时我们可以想一下，我们主进程一直在处理指令，而子进程在执行后台的保存，它会不会干扰到主进程的执行吗？

答案是不会，所以说他才是主流方案。子进程开始执行之后，它就会创建啊RDB文件把它存起来，操作完以后他会把这个结果返回，也就是说bgsave的过程分成两个过程，第一个是服务端拿到指令直接告诉客户端开始执行了；另外一个过程是一个子进程在完成后台的保存操作，操作完以后回一个消息。

#### 5.2.3 save配置自动执行

设置自动持久化的条件，满足限定时间范围内key的变化数量达到指定数量即进行持久化

```properties
save second changes
```

参数

second：监控时间范围

changes：监控key的变化量

范例：

```markdown
save 900 1
save 300 10
save 60 10000
```

其他相关配置：

```markdown
dbfilename filename
dir path
rdbcompression yes|no
rdbchecksum yes|no
stop-writes-on-bgsave-error yes|no
```

**save配置工作原理**

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/rdb%E5%90%AF%E5%8A%A8%E6%96%B9%E5%BC%8F.png)

#### 5.2.4 RDB三种启动方式对比

| 方式           | save指令 | bgsave指令 |
| -------------- | -------- | ---------- |
| 读写           | 同步     | 异步       |
| 阻塞客户端指令 | 是       | 否         |
| 额外内存消耗   | 否       | 是         |
| 启动新进程     | 否       | 是         |

**RDB特殊启动形式**

服务器运行过程中重启

```bash
debug reload
```

关闭服务器时指定保存数据

```bash
shutdown save
```

全量复制（在主从复制中详细讲解）



**RDB优点：**

- RDB是一个紧凑压缩的二进制文件，存储效率较高
- RDB内部存储的是redis在某个时间点的数据快照，非常适合用于数据备份，全量复制等场景
- RDB恢复数据的速度要比AOF快很多
- 应用：服务器中每X小时执行bgsave备份，并将RDB文件拷贝到远程机器中，用于灾难恢复。

**RDB缺点**

- RDB方式无论是执行指令还是利用配置，无法做到实时持久化，具有较大的可能性丢失数据
- bgsave指令每次运行要执行fork操作创建子进程，要牺牲掉一些性能
- Redis的众多版本中未进行RDB文件格式的版本统一，有可能出现各版本服务之间数据格式无法兼容现象



### 5.3  AOF

为什么要有AOF,这得从RDB的存储的弊端说起：

- 存储数据量较大，效率较低，基于快照思想，每次读写都是全部数据，当数据量巨大时，效率非常低
- 大数据量下的IO性能较低
- 基于fork创建子进程，内存产生额外消耗
- 宕机带来的数据丢失风险



那解决的思路是什么呢？

- 不写全数据，仅记录部分数据
- 降低区分数据是否改变的难度，改记录数据为记录操作过程
- 对所有操作均进行记录，排除丢失数据的风险

#### 5.3.1 AOF概念

**AOF**(append only file)持久化：以独立日志的方式记录每次写命令，重启时再重新执行AOF文件中命令 达到恢复数据的目的。**与RDB相比可以简单理解为由记录数据改为记录数据产生的变化**

AOF的主要作用是解决了数据持久化的实时性，目前已经是Redis持久化的主流方式

**AOF写数据过程**

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/rdb%E5%90%AF%E5%8A%A8%E6%96%B9%E5%BC%8F.png)

**启动AOF相关配置**

开启AOF持久化功能，默认no，即不开启状态

```properties
appendonly yes|no
```

AOF持久化文件名，默认文件名为appendonly.aof，建议配置为appendonly-端口号.aof

```properties
appendfilename filename
```

AOF持久化文件保存路径，与RDB持久化文件保持一致即可

```properties
dir
```

AOF写数据策略，默认为everysec

```properties
appendfsync always|everysec|no
```

#### 5.3.2 AOF执行策略

AOF写数据三种策略(appendfsync)

- **always**(每次）：每次写入操作均同步到AOF文件中数据零误差，性能较低，不建议使用。


- **everysec**（每秒）：每秒将缓冲区中的指令同步到AOF文件中，在系统突然宕机的情况下丢失1秒内的数据 数据准确性较高，性能较高，建议使用，也是默认配置


- **no**（系统控制）：由操作系统控制每次同步到AOF文件的周期，整体过程不可控

#### 5.3.3 AOF重写

场景：AOF写数据遇到的问题，如果连续执行如下指令该如何处理

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/rdb%E5%90%AF%E5%8A%A8%E6%96%B9%E5%BC%8F.png)

**什么叫AOF重写？**

随着命令不断写入AOF，文件会越来越大，为了解决这个问题，Redis引入了AOF重写机制压缩文件体积。AOF文件重 写是将Redis进程内的数据转化为写命令同步到新AOF文件的过程。简单说就是将对同一个数据的若干个条命令执行结 果转化成最终结果数据对应的指令进行记录。

**AOF重写作用**

- 降低磁盘占用量，提高磁盘利用率
- 提高持久化效率，降低持久化写时间，提高IO性能
- 降低数据恢复用时，提高数据恢复效率

**AOF重写规则**

- 进程内具有时效性的数据，并且数据已超时将不再写入文件


- 非写入类的无效指令将被忽略，只保留最终数据的写入命令

  如del key1、 hdel key2、srem key3、set key4 111、set key4 222等

  如select指令虽然不更改数据，但是更改了数据的存储位置，此类命令同样需要记录

- 对同一数据的多条写命令合并为一条命令

如lpushlist1 a、lpush list1 b、lpush list1 c可以转化为：lpush list1 a b c。

为防止数据量过大造成客户端缓冲区溢出，对list、set、hash、zset等类型，每条指令最多写入64个元素



**AOF重写方式**

- 手动重写

```properties
bgrewriteaof
```

**手动重写原理分析：**

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/rdb%E5%90%AF%E5%8A%A8%E6%96%B9%E5%BC%8F.png)



- 自动重写

```properties
auto-aof-rewrite-min-size size
auto-aof-rewrite-percentage percentage
```

自动重写触发条件设置

```properties
auto-aof-rewrite-min-size size
auto-aof-rewrite-percentage percent
```

自动重写触发比对参数（ 运行指令info Persistence获取具体信息 ）

```properties
aof_current_size  
aof_base_size
```

 自动重写触发条件公式：

![image-20200822225024451](https://gitee.com/wxqgm/pic/raw/master/javaweb/rdb%E5%90%AF%E5%8A%A8%E6%96%B9%E5%BC%8F.png)



#### 5.3.4 AOF工作流程及重写流程

![](https://gitee.com/wxqgm/pic/raw/master/javaweb/rdb%E5%90%AF%E5%8A%A8%E6%96%B9%E5%BC%8F.png)



![](https://gitee.com/wxqgm/pic/raw/master/javaweb/1.png)



![](https://gitee.com/wxqgm/pic/raw/master/javaweb/1.png)





### 5.4  RDB与AOF区别

#### 5.4.1 RDB与AOF对比（优缺点）

| 持久化方式   | RDB                | AOF                |
| ------------ | ------------------ | ------------------ |
| 占用存储空间 | 小（数据级：压缩） | 大（指令级：重写） |
| 存储速度     | 慢                 | 快                 |
| 恢复速度     | 快                 | 慢                 |
| 数据安全性   | 会丢失数据         | 依据策略决定       |
| 资源消耗     | 高/重量级          | 低/轻量级          |
| 启动优先级   | 低                 | 高                 |

#### 5.4.2 RDB与AOF应用场景

RDB与AOF的选择之惑

- 对数据非常敏感，建议使用默认的AOF持久化方案

AOF持久化策略使用everysecond，每秒钟fsync一次。该策略redis仍可以保持很好的处理性能，当出 现问题时，最多丢失0-1秒内的数据。

注意：由于AOF文件存储体积较大，且恢复速度较慢

- 数据呈现阶段有效性，建议使用RDB持久化方案

数据可以良好的做到阶段内无丢失（该阶段是开发者或运维人员手工维护的），且恢复速度较快，阶段 点数据恢复通常采用RDB方案

注意：利用RDB实现紧凑的数据持久化会使Redis降的很低，慎重总结：



**综合比对**

- RDB与AOF的选择实际上是在做一种权衡，每种都有利有弊
- 如不能承受数分钟以内的数据丢失，对业务数据非常敏感，选用AOF
- 如能承受数分钟以内的数据丢失，且追求大数据集的恢复速度，选用RDB
- 灾难恢复选用RDB
- 双保险策略，同时开启 RDB和 AOF，重启后，Redis优先使用 AOF 来恢复数据，降低丢失数据的量

```sh
# 客户端怎么访问都行
bind 0.0.0.0
port 6379
timeout 0
# 后台启动
daemonize yes
logfile "log_6379.log"
dir /usr/local/redis/data

# rdb 方式文件名称
dbfilename "dump6379.rdb"
# 存储策略 2s  中内如果有两个 修改命令 则执行存储
save 2 2

# aop 配置
appendonly yes
appendfilename "dump6379.aof"
appendfsync always

# aof 自动重写 
# 每4M 数据 重写一次
auto-aof-rewrite-min-size 4mb
# 和上次备份做对比,如果 超过了10% 则重写文件
auto-aof-rewrite-percentage 10%

```

