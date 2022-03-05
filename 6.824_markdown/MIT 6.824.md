# MIT 6.824

# 前期准备 

## 知乎常用相关链接

```
https://www.zhihu.com/question/29597104  # 如何的才能更好地学习 MIT6.824 分布式系统课程？
https://zhuanlan.zhihu.com/p/260470258   # MIT 6.824 分布式系统 | 材料准备和环境搭建
# 注意，它的git下来的东西有问题,用这个git
git clone git://g.csail.mit.edu/6.824-golabs-2021 6.824
```

## 课程助教友情提醒

```
https://thesquareplanet.com/blog/students-guide-to-raft/ # 原文
https://zhuanlan.zhihu.com/p/200903182 # 翻译1
https://zhuanlan.zhihu.com/p/203279804 # 翻译2
https://zhuanlan.zhihu.com/p/205315037 # 翻译3
```

## Lab1 参考

```
https://zhuanlan.zhihu.com/p/353356138 
https://zhuanlan.zhihu.com/p/372814816 
```

## 开场论文

```
https://pdos.csail.mit.edu/6.824/papers/mapreduce.pdf 
```

## 中文导读

```
https://mit-public-courses-cn-translatio.gitbook.io/mit6-824/lecture-01-introduction
```

# 第一周任务：

## 课程介绍

```
什么是分布式系统?
多个电脑合作
大型网站存储，MapReduce，点对点共享，等等
许多关键的基础设施是分布式的

为什么人们要构建分布式系统?
通过并行性增加容量
通过复制容忍错误
使计算在物理上接近外部实体
通过隔离实现安全

但是:
许多并发部分，复杂的交互
必须应对部分失败
技巧性实现表现潜力

为什么选这门课?
很有趣——困难的问题，强大的解决方案
由真正的系统所使用——由大型Web站点的崛起所驱动
活跃的研究领域——重要的未解决问题
动手操作——您将在实验室中构建真正的系统

课程结构

http://pdos.csail.mit.edu/6.824

课程组件:
讲座
论文
两个考试
实验室
最终项目(可选)

讲座:
伟大的想法，论文讨论和实验室
会被录下来，在网上可以看到吗

论文:
研究论文，有经典的，也有新的
问题、想法、实施细节、评估
很多讲座都集中在论文上
请在课前阅读论文!
每篇论文都有一个简短的问题让你回答
我们要求你们把关于论文的问题发给我们
在前一晚的午夜前提交问题和答案


实验室:
目标:加深对一些重要技术的理解
目标:有分布式编程的经验

实验1:MapReduce
实验2:使用Raft进行容错复制
实验3:容错的键/值存储
实验4:分片键/值存储

主要议题

这是一门关于应用程序基础设施的课程。
*存储。
*沟通。
*计算。

主要目标是:隐藏分布复杂性的抽象。
在我们的搜索中，有几个话题会反复出现。

主题:实现
RPC，线程，并发控制。
实验室……

主题:性能
目标:可伸缩的吞吐量
Nx服务器-> Nx通过并行CPU、磁盘、网络的总吞吐量。
【图:用户、应用服务器、存储服务器】
因此，处理更多的负载只需要购买更多的计算机。
而不是由昂贵的程序员重新设计。
当你可以在没有太多互动的情况下划分工作时，效率就会提高。
随着N的增加，缩放变得困难:
负载不平衡，滞后，n次延迟。
非并行代码:初始化、交互。
共享资源的瓶颈，例如网络。
一些性能问题不容易通过扩展来解决
例如，单个用户请求的快速响应时间
例如，所有用户都想更新相同的数据
通常需要更好的设计，而不仅仅是更多的电脑
实验室4

主题:容错
成千上万的服务器，庞大的网络，总是有些东西坏掉
我们希望对应用程序隐藏这些失败。
我们经常想:
可用性——应用可以在失败的情况下取得进展
可恢复性——当故障修复后，应用程序将恢复正常
伟大的想法:复制服务器。
如果一个服务器崩溃，可以继续使用其他服务器。
1 2 3号实验室

主题:一致性
通用基础设施需要定义良好的行为。
如。Get(k)从最近的看跌期权(k,v)中获得价值。
养成良好的行为是很难的!
“复制”服务器很难保持一致。
客户端可能在多步骤更新过程中崩溃。
服务器可能会崩溃，例如在执行后，但在回复之前。
网络分区可能会使活跃的服务器看起来死气;“脑裂”的风险。
一致性和绩效是敌人。
强烈的一致性需要沟通，
Get()必须检查最近的Put()。
为了提高速度，许多设计只提供了较弱的一致性。
Get()不*不*生成最新的Put()!
这对应用程序程序员来说是痛苦的，但可能是一种好的权衡。
在一致性/性能谱中可能有许多设计点!

案例研究:MapReduce

让我们以MapReduce (MR)为例
这是6.824主要主题的一个很好的说明
巨大的影响力
实验1的重点

MapReduce的概述
上下文:在多tb数据集上进行数小时计算
例如建立搜索索引，或分类，或分析网络结构
只适用于1000台电脑
应用程序不是由分布式系统专家编写的
总体目标:对非专业程序员来说很容易
程序员只是定义了Map和Reduce函数
通常是相当简单的顺序代码
MR负责并隐藏分发的所有方面!

MapReduce任务的抽象视图
输入(已经)被分割成M个文件
Input1 -> Map -> a， >，1
input t2 -> Map -> b,1
Input3 -> Map -> a, 1c,1
|   |   |
| | -> Reduce -> c,1
| -----> Reduce -> b,2
---------> Reduce -> a,2
MR为每个输入文件调用Map()，生成k2,v2的集合
“中间”数据
每个Map()调用都是一个“任务”。
对于给定的k2, MR收集所有的中间v2，
并将每个键+值传递给Reduce调用
最后的输出是Reduce()s中<k2,v3>对的集合

例如:单词计数
输入是数以千计的文本文件
地图(k、v)
把v拆成文字
对于每个单词w
发出(w,“1”)
减少(k、v)
发出(len (v))

MapReduce尺度:
N个“工人”计算机给你带来Nx的吞吐量。
map()可以并行运行，因为它们不交互。
同样减少()。
所以你可以通过购买更多的计算机来获得更多的吞吐量。

MapReduce隐藏了很多细节:
向服务器发送应用程序代码
跟踪完成了哪些任务
将数据从Maps移动到reduce
在服务器上平衡负载
从失败中恢复

然而，MapReduce限制了应用程序的功能:
没有交互或状态(除了通过中间输出)。
没有迭代，没有多级管道。
没有实时或流处理。

输入和输出存储在GFS集群文件系统上
MR需要巨大的并行输入和输出吞吐量。
GFS在许多服务器上以64 MB的块分割文件
并行读取地图
减少并行写
GFS还在2或3个服务器上复制每个文件
拥有GFS是MapReduce的一大胜利

什么可能会限制性能?
因为这是需要优化的。
CPU ?记忆?磁盘吗?网络?
在2004年，作者受到了网络容量的限制。
MR通过网络发送什么?
地图从GFS读取输入。
减少读取Map输出。
可以与输入一样大，例如排序。
减少将输出文件写入GFS。
[图:服务器，网络交换机树]
在MR的all-to-all shuffle中，有一半的流量通过根交换机。
纸张的根开关:总计100到200千兆/秒
1800台机器，55兆/秒/台。
55是小的，例如远低于磁盘或RAM的速度。
今天:网络和根交换机比CPU/磁盘快得多。

一些细节(论文图1):
一个主人，把任务分配给工人并记住进度。
1. master将Map任务交给worker，直到所有Map任务完成
将写输出(中间数据)映射到本地磁盘
将分割输出按哈希映射到每个Reduce任务的一个文件中
2. 在所有的map完成后，大师分发Reduce任务
每个Reduce从(所有)Map worker获取中间输出
每个Reduce任务在GFS上写一个单独的输出文件

MR如何最小化网络使用?
Master尝试在存储其输入的GFS服务器上运行每个Map任务。
所有的电脑都同时运行GFS和MR工作系统
因此，输入是从本地磁盘读取(通过GFS)，而不是通过网络。
中间数据只经过一次网络。
映射工作者写入本地磁盘。
减少工人直接从地图工人读取，而不是通过GFS。
中间数据被分割成包含许多键的文件。
R比键的个数要小得多。
大的网络传输效率更高。

MR如何获得良好的负载平衡?
如果N-1个服务器必须等待1个慢速服务器完成，则是浪费和缓慢的。
但有些任务可能比其他任务耗时更长。
解决方法:比工人更多的任务。
大师将新任务分配给完成之前任务的工人。
所以没有任务大到超过完成时间。
所以速度快的服务器比速度慢的服务器做更多的任务，完成的时间差不多相同。

那么容错呢?
例如，如果一个工人在MR工作期间发生车祸怎么办?
我们希望对应用程序程序员完全隐藏失败!
MR必须从头开始重新运行整个工作吗?
为什么不呢?
MR只会重新运行失败的Map()和Reduce()。
假设MR运行一个Map两次，一个Reduce看到第一次运行的输出，
另一个Reduce看到第二次运行的输出?
正确性要求重新执行以产生完全相同的输出。
所以Map和Reduce必须是纯确定性函数:
他们只能看到自己的论点。
没有状态，没有文件I/O，没有交互，没有外部通信。
如果您想要允许非功能性的Map或Reduce呢?
工作失败需要重新执行整个工作，
或者需要创建同步的全局检查点。

工人崩溃恢复的详细信息:
*地图工人崩溃:
Master通知worker不再响应ping
master知道它在那个worker上运行了哪些Map任务
这些任务的中间输出现在丢失了，必须重新创建
Master告诉其他工人去执行这些任务
如果reduce已经获取了中间数据，可以忽略重新运行吗
*减少工人崩溃。
完成的任务是可以的——存储在GFS中，带有副本。
大师将工人未完成的任务重新放到其他工人身上。

其他故障/问题:
*如果master给两个worker相同的Map()任务怎么办?
也许是主人误以为一个工人死了。
它只会告诉Reduce workers其中一个。
如果master给两个worker相同的Reduce()任务怎么办?
它们都将尝试在GFS上写入相同的输出文件!
原子GFS重命名防止混合;一个完整的文件将可见。
*如果有一个员工非常慢——“掉队的人”怎么办?
也许是因为古怪的硬件。
Master开始最后几个任务的第二份副本。
*如果一个工人计算错误的输出，由于坏的h/w或s/w?
太糟糕了!MR假设cpu和软件是“故障停止”的。
*如果主机崩溃怎么办?

当前状态?
具有巨大的影响力(Hadoop、Spark等)。
可能已经不在谷歌使用了。
由Flume / FlumeJava代替(见Chambers等人的论文)。
GFS被Colossus(没有好的描述)和BigTable取代。

结论
MapReduce单枪匹马让大集群计算流行起来。
-不是最有效或最灵活的。
+良好的可扩展性。
+易于编程—故障和数据移动是隐藏的。
这些在实践中都是很好的权衡。
在后面的课程中，我们会看到一些更先进的后继者。
祝你在实验室玩得开心!
```

## 第一周论文原文和翻译

```
原文：http://nil.csail.mit.edu/6.824/2020/papers/mapreduce.pdf
翻译：https://www.cnblogs.com/fuzhe1989/p/3413457.html
翻译：https://zhuanlan.zhihu.com/p/122571315
```

```
今天:
Go中的线程和RPC，着眼于实验室

为什么是Go?
对线程的良好支持
方便的RPC
类型安全
垃圾回收(释放问题后无用)
线程+ GC是特别吸引人的!
相对简单的
学习完本教程后，使用https://golang.org/doc/effective_go.html

线程
这是一个有用的结构化工具，但可能比较棘手
Go把它们叫做goroutines;其他人都称它们为线程

线程=“执行线程”
线程允许一个程序同时做许多事情
每个线程串行执行，就像普通的非线程程序一样
线程共享内存
每个线程包含一些每个线程的状态:
程序计数器，寄存器，堆栈

为什么线程呢?
它们表达了分布式系统中所需要的并发性
I / O并行性
客户端并行地向多个服务器发送请求，并等待响应。
服务器处理多个客户端请求;每个请求都可能被阻塞。
在等待磁盘读取客户端X的数据时，
处理客户端Y的请求。
多核性能
在几个核心上并行执行代码。
方便
在后台，每秒一次，检查每个worker是否还活着。

除了线程还有别的选择吗?
可以:在单个线程中，明确地编写交叉活动的代码。
通常被称为“事件驱动”。
保持一个关于每个活动的状态表，例如每个客户请求。
一个“事件”循环是:
检查每个活动的新输入(例如服务器回复的到达)，
每个活动的下一步，
更新状态。
事件驱动的I/O并发，
并消除线程成本(这可能是相当大的)，
但它没有多核加速，
编程是很痛苦的。

线程的挑战:
共享数据
例如，如果两个线程同时执行n = n + 1，会发生什么?
或者一个线程读取而另一个线程递增?
这是一场“比赛”——通常是一个漏洞
->使用锁(Go的sync.Mutex)
->或避免共享可变数据
线程之间的协调
例如，一个线程产生数据，另一个线程消费数据
消费者如何等待(并释放CPU)?
生产者如何唤醒消费者?
->使用Go通道或同步。sync.Cond或WaitGroup
死锁
循环通过锁和/或通信(例如RPC或Go通道)

让我们以线程为例来看看本教程的web爬虫。

什么是网络爬虫?
目标是获取所有网页，例如提供给索引器
网页和链接形成一个图表
多个链接到某些页面
图有周期

履带的挑战
利用I / O并行性
网络延迟比网络容量更有限制
同时获取多个url
增加每秒获取的url数
=>需要并发线程
只获取每个URL *一次*
避免浪费网络带宽
善待远程服务器
=>需要记住访问的url
知道什么时候完成

我们将看看两种类型的解决方案[爬虫。进入日程页面]

串行爬虫:
通过递归串行调用执行深度优先的探测
“获取”的地图避免了重复，打破了循环
一个映射，通过引用传递，调用者看到被调用者的更新
但是:每次只取一个页面
我们可以在Serial()调用前放一个“go”吗?
让我们试试……发生了什么事?

ConcurrentMutex爬虫:
为每个获取页面创建一个线程
许多并发读取，更高的读取速率
“go func”创建一个goroutine并开始运行
函数…是一个“匿名函数”
线程共享“获取的”映射
因此，只有一个线程会获取任何给定的页面
为什么使用互斥锁(Lock()和Unlock())?
一个原因:
两个不同的网页包含指向相同URL的链接
两个线程同时获取这两个页面
T1读取[url]， T2读取[url]
两者都看到url还没有被获取(already == false)
两者都取，这是错误的
锁导致检查和更新是原子的
所以只有一个线程看到already==false
另外一个原因:
在内部，map是一个复杂的数据结构(树?可扩展的散列?)
并发更新/更新可能破坏内部不变量
并发更新/读取可能导致读取崩溃
如果我注释掉Lock() / Unlock()呢?
去运行crawler.go
为什么会这样?
跑啊，跑跑，快跑
即使输出正确，也要检测竞争!
ConcurrentMutex爬虫如何决定它已经完成?
同步。WaitGroup
Wait()等待所有的Add()被Done()平衡
即等待所有子线程完成
[图:goroutines树，覆盖在循环URL图上]
树中的每个节点都有一个WaitGroup
这个爬虫程序可能创建多少并发线程?

ConcurrentChannel履带
一个通道:
通道是一个对象
Ch:= make(chan int)
通道允许一个线程向另一个线程发送一个对象
ch < - x
发送方等待，直到某个gorroutine收到
Y:= <- ch
对于y:=范围ch
接收器等待，直到某个goroutine发送
信道既通信又同步
多个线程可以在一个通道上发送和接收
通道是便宜的
记住:发送方阻塞直到接收方收到!
“同步”
小心死锁
ConcurrentChannel大师()
Master()创建一个worker goroutine来获取每个页面
worker()在一个通道上发送页面的url片段
多个工作器在单个通道上发送
master()从通道读取URL片
主人在第几排等候?
主机在等待时是否使用CPU时间?
不需要锁定获取的映射，因为它不是共享的!
主人怎么知道已经完成了呢?
在n中保持工人计数。
每个worker在channel上只发送一个项目。

为什么多个线程使用同一个通道不是竞争?

当工作线程写入一个url片段时，是否存在竞争，
而主线程读取该片，而不锁定?
* worker只在发送之前写slice
* master只在*接收后读取slice *
所以他们不能同时用这片。

什么时候使用共享和锁，而不是通道?
大多数问题都可以用这两种方式解决
什么是最有意义的取决于程序员的想法
状态共享和锁
沟通——渠道
对于6.824实验室，我建议共享+锁的状态，
和同步。Cond或channel或time.Sleep()用于等待/通知。

远程过程调用(RPC)
分布式系统的关键部件;所有的实验室都使用RPC
目标:易于编程的客户端/服务器通信
隐藏网络协议的细节
将数据(字符串、数组、映射等)转换为“有线格式”

RPC消息图:
客户端              服务器
请求- >
<——反应

软件结构
客户端应用程序处理程序FNS
存根fn调度员
RPC库
净   ------------ 净

例子:kv。进入日程表页面
一个玩具键/值存储服务器——Put(键，值)，Get(键)->值
使用Go的RPC库
常见的:
为每个服务器处理器声明Args和Reply结构。
客户:
connect()的Dial()创建一个到服务器的TCP连接
Get()和put()是客户端“存根”
调用()请求RPC库执行调用
您指定服务器函数名，参数，放置应答的位置
库封送参数，发送请求，等待，解封回复
从Call()返回值表明它是否得到了一个回复
通常你也会得到回复。表示服务级别故障的错误
服务器:
Go要求服务器声明一个带有RPC处理程序方法的对象
然后服务器向RPC库注册该对象
服务器接受TCP连接，将它们交给RPC库
RPC库
读取每个请求
为这个请求创建一个新的goroutine
解组请求
查找指定对象(在表create by Register()中)
调用对象的命名方法(分派)
马歇尔的回复
在TCP连接上写应答
服务器的Get()和Put()处理程序
必须锁定，因为RPC库为每个请求创建一个新的goroutine
阅读参数;修改回复

一些细节:
绑定:客户端如何知道要与哪台服务器计算机通信?
对于Go的RPC，服务器名/端口是Dial的参数
大系统有某种名称或配置服务器
编组:将数据格式化成数据包
Go的RPC库可以传递字符串、数组、对象、映射等等
Go通过复制指向的数据来传递指针
无法传递通道或函数

RPC问题:如何处理失败?
例如丢包，网络故障，服务器速度慢，服务器崩溃

对于客户机RPC库来说，失败是什么样子的?
客户端永远不会看到来自服务器的响应
客户端不知道服务器是否看到了请求!
[各点损失图]
也许服务器没有看到这个请求
也许服务器执行了，在发送回复之前崩溃了
也许服务器执行了，但是在发送回复之前，网络中断了

最简单的故障处理方案:“尽力而为”
Call()等待响应一段时间
如果没有到达，重新发送请求
这样做几次
然后放弃并返回一个错误

问:应用程序很容易处理“最佳效果”吗?

一种特别糟糕的情况:
客户端执行
(“k”,10);
(“k”,20);
两个成功
得到("k")的收益是多少?
【图、超时、重发、原到晚】

问:尽最大的努力可以吗?
只读操作
重复时不做任何操作的操作
DB检查记录是否已经被插入

更好的RPC行为:“最多一次”
思想:服务器RPC代码检测重复的请求
返回先前的应答，而不是重新运行处理程序
问:如何检测重复的请求?
客户端在每个请求中包含唯一ID (XID)
使用相同的XID重新发送
服务器:
如果看到(xid):
r =老(xid)
其他的
r =处理程序()
老(xid) = r
(xid) = true

一些至多一次复杂性
这在第三实验室会讲到
如果两个客户机使用相同的XID怎么办?
大随机数?
将唯一的客户端ID (ip地址?)和序列#?
服务器最终必须丢弃关于旧rpc的信息
什么时候丢弃是安全的?
的想法:
每个客户端都有一个唯一的ID(可能是一个很大的随机数)
每个客户端RPC序列号
客户端包含“看到所有的回复<= X”与每个RPC
很像TCP序列#s和ack
或者一次只允许客户端使用一个未完成的RPC
到达seq+1允许服务器丢弃所有<= seq
如何处理dup请求，而原始仍在执行?
服务器还不知道回复
每个执行RPC的“挂起”标志;等待或忽略

如果最多只有一次服务器崩溃并重新启动怎么办?
如果在内存中最多重复一次信息，服务器将忘记
并且在重新启动后接受重复的请求
也许它应该把重复的信息写到磁盘上
也许副本服务器也应该复制副本信息

RPC是“最多一次”的简单形式
开放的TCP连接
写请求到TCP连接
RPC从不重新发送请求
所以服务器不会看到重复的请求
如果它没有得到一个回复，RPC代码返回一个错误
可能在超时之后(来自TCP)
可能服务器没有看到请求
也许服务器处理了请求，但是服务器/网络在回复之前失败了

那么“恰好一次”呢?
无界重试，重复检测，容错服务
实验室3
```





```
去常见问题解答

问:为什么6.824在实验室中使用Go ?

答:直到几年前，6.824还使用c++，它工作得很好。去工作
由于几个原因，6.824 LABS稍好一些。是垃圾
收集的和类型安全的，这消除了一些常见的bug类。
Go对线程(goroutines)有很好的支持，还有一个很好的RPC包，
它们在6.824中是直接有用的。线程和垃圾收集
因为垃圾收集可以，所以一起工作特别好
消除了程序员决定最后一个线程使用
对象已停止使用它。其他语言也有这些
这些特性可能在6.824实验室中工作得很好，比如Java。

问:goroutines是并行运行的吗?你能用它们来增加吗
性能?

A: Go的goroutines和其他语言中的线程是一样的。去的
运行时在所有可用的内核上并行执行goroutines。如果
内核比可运行的goroutines少，运行时将会
在goroutines之间抢占时间共享内核。

问:Go频道如何工作?去怎么确定他们是
在许多可能的goroutines之间同步?

A:你可以在https://golang.org/src/runtime/chan.go上看到源代码，
虽然这并不容易遵循。

在高层次上，chan是一个包含一个缓冲区和一个锁的结构体。
在通道上发送锁需要获取锁、等待(也许
释放CPU)，直到某个线程正在接收，然后处理
消息。接收包括获取锁和等待
发送者。你可以实现自己的通道与Go同步。互斥锁,
sync.Cond。

Q:我正在使用一个通道唤醒另一个goroutine，通过发送一个
通道上的哑bool值。但如果另一个gorroutine已经
运行(因此不在通道上接收)发送的gor例程
块。我该怎么办?

答:尝试条件变量(Go的sync.Cond)而不是通道。
条件变量可以很好地提醒goroutines，它可能(或可能
不是)等待某件事。频道，因为它们是同步的，
如果你不确定是否会有一场约会，你会觉得尴尬吗
通道的另一端。

Q:我如何能有一个goroutine等待输入的任何一个数字
不同的渠道?试图接收任何一个频道块，如果
没有什么可读的，防止goroutine检查其他
频道。

A:尝试为每个通道创建一个单独的goroutine，并拥有每个通道
Goroutine块在它的通道上。这并不总是可能的，但当
这是最简单的方法。

否则试试Go的选择。

问:什么时候应该使用同步。WaitGroup而不是频道?反之亦然?

答:WaitGroup的目的相当特殊;它只有在等待的时候才有用
一堆活动要完成。渠道更
通用的;例如，你可以传递价值观
频道。你可以等待多个goroutines使用通道，尽管它
需要比使用WaitGroup多几行代码。

Q:我需要我的代码每秒执行一次任务。是什么
最简单的方法是什么?

A:为定期任务创建一个gorroutine。它应该有
使用time.Sleep()暂停一秒钟，然后执行
task，然后循环到time.Sleep()。

Q:我们怎么知道什么时候生成goroutines的开销超过
我们从他们身上获得的并行性?

答:这取决于!如果您的机器有16个核，并且您正在寻找
CPU并行性，你应该有大约16个可执行的goroutines。如果
它需要0.1秒的实时时间来获取一个网页和你的网络
能够每秒传输100个网页，你可能需要
为了使用所有的
网络容量。实验上，当你增加
在一段时间内，您会看到高例程增加了吞吐量，然后
你将不再获得更多的吞吐量;这样你就有足够的钱了
从性能的角度来看Goroutines。

问:如何创建一个通过互联网连接的Go通道?
如何指定用来发送消息的协议?

答:一个Go频道只能在一个程序中工作;渠道不能
用于与其他程序或其他计算机交谈。

看看Go的RPC包，它可以让你和其他Go
互联网上的程序:

https://golang.org/pkg/net/rpc/

问:有哪些重要/有用的go特定并发模式需要了解?

答:这是一个关于这个话题的幻灯片，来自一个go专家:

https://talks.golang.org/2012/concurrency.slide

Q:如何实现切片?

A: slice是一个对象，它包含一个指向数组的指针和一个开始和
数组的结束索引。这种安排允许多个切片
共享一个底层数组，每个片可能暴露一个不同的
数组元素的范围。

下面是一个更深入的讨论:

https://blog.golang.org/go-slices-usage-and-internals

我经常使用切片，而从不使用数组。go切片比
一个Go数组，因为数组的大小是其类型的一部分
以一个切片作为参数的函数可以取任意的切片
长度。

问:人们在使用Go时常用的调试工具是什么?

问:fmt.Printf ()

据我所知，Go还没有一个很好的调试器，尽管gdb可以
工作:

https://golang.org/doc/gdb

在任何情况下，对于大多数错误，我发现fmt.Printf()是一个极端
有效的调试工具。

Q:什么时候使用同步RPC调用是正确的，什么时候是正确的
使用异步RPC调用?

答:大多数代码需要RPC回复才能继续;在这种情况下
使用同步RPC是有意义的。

但是有时候客户端想要启动许多并发的rpc;在那
大小写异步可能更好。或者客户想在此期间做其他工作
等待RPC完成，可能是因为服务器在很远的地方
(所以光速高)或者因为服务器可能不是
可达的，以便RPC承受很长的超时时间。

我从来没有在Go中使用异步RPC。当我想发送一个RPC，但不是
必须等待结果，我创建一个goroutine，并有
goroutine做一个同步调用()。

问:工业中使用go吗?

答:你可以看到估计有多少不同的编程语言
使用:

https://www.tiobe.com/tiobe-index/
```

```
For each paper, your assignment is two-fold. By midnight the night before lecture:

Submit your answer for each lecture's paper question via the submission web site, and
Submit your own question about the paper (e.g., what you find most confusing about the paper or the paper's general context/problem). You cannot use the question below. To the extent possible, during lecture we will try to answer questions submitted the evening before.
You can also upload your questions and answers using curl:

## Answer goes into lecN.txt
$ curl -F file=@lec2.txt \
       -F key=XXXXXXXX \
       https://6824.scripts.mit.edu/2020/handin.py/upload
## Question goes into sqN.txt
$ curl -F file=@sq2.txt \
       -F key=XXXXXXXX \
       https://6824.scripts.mit.edu/2020/handin.py/upload
Lecture 2

The assigned reading for today is not a paper, but the Online Go tutorial. The assigned "question" is the Crawler exercise in the tutorial. Also, take a look at Go's RPC package, which you will use in lab 1.

Questions or comments regarding 6.824? Send e-mail to 6824-staff@lists.csail.mit.edu.

Top // 6.824 home //

每篇论文的作业分为两部分。讲座前的午夜:

请通过提交网站提交每堂课论文问题的答案
提交你自己关于论文的问题(例如，你觉得论文中最令人困惑的地方或论文的一般背景/问题)。你不能用下面的问题。在讲座中，我们将尽可能回答前一晚提交的问题。
你也可以使用curl上传你的问题和答案:

##答案进入lecN.txt
$ curl -F file=@lec2.txt \
- f键= XXXXXXXX \
https://6824.scripts.mit.edu/2020/handin.py/upload
##问题进入sqN.txt
$ curl -F file=@sq2.txt \
- f键= XXXXXXXX \
https://6824.scripts.mit.edu/2020/handin.py/upload
讲座2

今天的阅读作业不是论文，而是在线go教程。指定的“问题”是本教程中的Crawler练习。另外，看看Go的RPC包，您将在实验1中使用它。

关于6.824的问题或评论?请发送电子邮件至6824-staff@lists.csail.mit.edu。
```

```
# lab1 http://nil.csail.mit.edu/6.824/2020/labs/lab-mr.html
介绍

在本实验中，您将构建一个MapReduce系统。您将实现一个工作进程，它调用应用程序Map和Reduce函数并处理读写文件，以及一个主进程，它将任务分发给工作人员并处理失败的工作人员。您将构建类似于MapReduce论文的东西。

合作政策

你们必须写出你们交上去的6.824的所有代码，除了我们布置给你们的部分代码。你不允许看其他人的解，也不允许看前几年的解。你可以和其他学生讨论作业，但你不能互相查看或复制别人的代码。制定这一规则的原因是，我们相信，通过自己设计和实现实验室解决方案，您将学到最多的东西。

请不要发布您的代码，也不要让当前或未来的6.824学生可以使用它。Github.com的存储库默认是公开的，所以请不要把你的代码放在那里，除非你把存储库变成私有的。你可能会发现使用MIT的GitHub很方便，但一定要创建一个私有的存储库。

软件

你将在Go中实现这个实验室(以及所有的实验室)。Go网站上有很多教程信息。我们将使用Go 1.13版本对您的实验室进行评分;你也应该使用1.13。你可以通过运行Go version来检查你的Go版本。

我们建议您在自己的机器上进行实验室工作，这样您就可以使用您已经熟悉的工具、文本编辑器等。或者，你可以在雅典娜的实验室工作。

macOS

你可以使用Homebrew来安装Go。安装完Homebrew后，运行brew install go。


设置ggo美元
开始

您将使用git(一个版本控制系统)获取初始的实验室软件。要了解更多关于git的知识，请参阅Pro git的书籍或git用户手册。获取6.824实验室软件:

$ git clone git://g.csail.mit.edu/6.824-golabs-2020 6.824
$ cd 6.824
$ ls
Makefile src
＄
我们在src/main/ mrsequence .go中提供了一个简单的顺序mapreduce实现。它在单个进程中运行映射并一次减少一个映射。我们还提供了几个MapReduce应用程序:在mrapps/wc中统计单词。然后在mrapps/indexer. Go中添加一个文本索引器。你可以按如下顺序运行单词计数:

cd ~ / 6.824美元
$ cd src /主要
$ go build -buildmode=plugin ../mrapps/wc.go
美元rm mr-out *
$运行mrsequential命令。去厕所。所以pg * . txt
更多的美元mr-out-0
一个509
约2
法案8
.．.
mrsequential。Go将其输出留在文件mr-out-0中。输入来自名为pg-xxx.txt的文本文件。

请随意从mrsequential.go借用代码。你也应该看看mrapps/wc。去看看MapReduce应用程序的代码是什么样的。

你的工作

你的工作是实现一个分布式的MapReduce，由两个程序组成，主程序和工作程序。只有一个主进程，一个或多个工作进程并行执行。在一个真实的系统中，工作人员会在许多不同的机器上运行，但在这个实验室中，你会在一台机器上运行它们。worker将通过RPC与master对话。每个工作进程向主进程请求一个任务，从一个或多个文件中读取任务的输入，执行任务，并将任务的输出写入一个或多个文件。如果一个工人没有在合理的时间内完成任务(对于这个实验室，用10秒)，主人应该注意到，并将相同的任务交给不同的工人。
我们给了你一些开始的代码。master和worker的“main”例程在main/mrmaster中。去主要/ mrworker.go;不要更改这些文件。你应该把你的实现放在mr/master中。去,先生/工人。去吧,先生/ rpc.go。

下面介绍如何在单词计数MapReduce应用程序上运行代码。首先，确保单词计数插件是新构建的:

$ go build -buildmode=plugin ../mrapps/wc.go
在主目录下，运行master。
美元rm mr-out *
$ go run mrmaster。pg - * . txt
将pg-*.txt参数传递给mrmaster。Go是输入文件;每个文件对应一个“split”，是一个Map任务的输入。
在一个或多个其他窗口中，运行一些worker:

$去运行mrworker。去wc.so
当工人和师傅完成后，查看mr-out-*的输出。当你完成了实验，输出文件的排序并集应该匹配顺序输出，像这样:
$ cat mr-out-* |排序|更多
一个509
约2
法案8
.．.
我们在main/test-mr.sh中提供了一个测试脚本。当给定pg-xxx.txt文件作为输入时，测试检查wc和索引器MapReduce应用程序是否产生正确的输出。测试还检查您的实现是否并行运行Map和Reduce任务，以及您的实现是否从运行任务时崩溃的工人中恢复。

如果你现在运行测试脚本，它会挂起，因为master永远不会结束:

$ cd ~ / 6.824 / src /主要
sh test-mr.sh美元
***开始wc测试。
你可以在mr/master的Done函数中将ret:= false更改为true。快去，让主人马上离开。然后:

sh美元。/ test-mr.sh
***开始wc测试。
排序:没有这样的文件或目录
cmp: mr-wc-all的EOF
——wc输出与mr-correct-wc.txt不一样
wc测试:FAIL
＄
测试脚本希望看到名为mr-out-X的文件中的输出，每个reduce任务对应一个文件。mr/master的空实现。去先生/工人。Go不要生成这些文件(或者做其他很多事情)，这样测试就会失败。

当你完成的时候，测试脚本的输出应该是这样的:

sh美元。/ test-mr.sh
***开始wc测试。
wc测试:通过
***启动索引器测试。
——indexer测试:PASS
***开始映射并行度测试。
——地图并行度测试:通过
***开始减少并行度测试。
——reduce parallelism test: PASS
***开始碰撞测试。
—碰撞测试:通过
***通过所有测试
＄
您还将看到Go RPC包中的一些错误

2019/12/16 13:27:09 rpc。Register:方法Done有1个输入参数;需要确切的三
忽略这些消息。

一些规则:

map阶段应该将中间键划分为nReduce reduce任务的bucket，其中nReduce是main/mrmaster的参数。go传递到MakeMaster()。
worker实现应该将第X个reduce任务的输出放在文件mr-out-X中。
mr-out-X文件应该在每个Reduce函数输出中包含一行。该行应该以Go“%v %v”格式生成，用键和值调用。看看主序/主序。找到注释“这是正确的格式”的那行。如果您的实现过多地偏离这种格式，那么测试脚本将会失败。
可以修改mr/worker。去,先生/主人。去吧,先生/ rpc.go。你可以临时修改其他文件进行测试，但要确保你的代码与原始版本兼容;我们将用原始版本进行测试。
worker应该将中间的Map输出放在当前目录中的文件中，稍后您的worker可以将它们作为Reduce任务的输入读取到其中。
主要/ mrmaster。希望先生/主。go去实现Done()方法，当MapReduce任务完全完成时返回true;在这点上，马斯特先生。将退出去。
当作业完全完成时，工作进程应该退出。实现这一点的一个简单方法是使用call()的返回值:如果worker没有联系到master，它可以假设master已经退出，因为工作已经完成，所以worker也可以终止。根据您的设计，您可能还会发现有一个“请退出”的伪任务很有帮助，master可以将这个伪任务交给worker。
提示

一种开始的方法是修改mr/worker。go的Worker()发送一个RPC到master请求一个任务。然后修改主服务器，以使用尚未启动的映射任务的文件名进行响应。然后修改worker以读取该文件并调用应用程序的Map函数，如mrsequentil .go。
应用程序Map和Reduce函数是在运行时使用Go插件包从以so结尾的文件中加载的。
如果你改变mr/目录中的任何东西，你可能不得不重新构建你所使用的任何MapReduce插件，例如go build -buildmode=plugin ../mrapps/wc.go
这个实验室依赖于工作人员共享一个文件系统。当所有工作器都运行在同一台机器上时，这很简单，但如果工作器运行在不同的机器上，就需要全局文件系统，比如GFS。
中间文件的合理命名约定是mr-X-Y，其中X是Map任务号，Y是reduce任务号。
worker的map任务代码需要一种将中间键/值对存储在文件中的方法，这种方法可以在reduce任务期间正确地读取回来。一种可能是使用Go的encoding/json包。将键/值对写入JSON文件:
内附:= json.NewEncoder(文件)
对于_，kv:=…｛
错:= enc.Encode (kv)
要读回这样的文件:
12月:= json.NewDecoder(文件)
为{
var kv KeyValue
if err:= dec.Decode(&kv);Err != nil {
打破
｝
附加(Kva, kv)
｝
worker的map部分可以使用ihash(key)函数(在worker.go中)为给定的键选择reduce任务。
你可以从mrsequential中窃取一些代码。用于读取Map输入文件，排序Map和Reduce之间的中间键/值对，并将Reduce输出存储在文件中。
主服务器，作为一个RPC服务器，将是并发的;不要忘记锁定共享数据。
使用围棋的比赛探测器，去建立-比赛和去跑-比赛。Test-mr.sh有一条注释，说明如何为测试启用竞赛检测器。
工人有时需要等待，例如，减少不能开始，直到最后一个地图完成。一种可能是工人定期向主人请求工作，在每个请求之间睡觉。另一种可能性是，主服务器中的相关RPC处理程序有一个等待的循环，要么是time.Sleep()，要么是sync.Cond。Go在自己的线程中为每个RPC运行处理程序，因此一个处理程序正在等待的事实不会阻止master处理其他RPC。
master无法可靠地区分崩溃的工人，活着但由于某种原因而停止的工人，以及执行任务但太慢而无法发挥作用的工人。您能做的最好的事情是让master等待一段时间，然后放弃并将任务重新发给另一个worker。这个实验室，让主人等十秒钟;在此之后，主人应该假定工人已经死亡(当然，也可能没有)。
要测试崩溃恢复，你可以使用mrapps/crash。应用程序的插件。它随机存在于Map和Reduce函数中。
为了确保在崩溃时没有人注意到部分写入的文件，MapReduce论文提到了使用临时文件的技巧，并在文件完全写入后自动重命名它。你可以使用ioutil。创建临时文件和操作系统。重命名为原子重命名。
Test-mr.sh运行子目录mr-tmp中的所有进程，因此，如果出现问题，您需要查看中间文件或输出文件，请查看那里。
Handin过程

在提交之前，请最后一次运行test-mr.sh。

使用make lab1命令将你的实验作业打包并上传到课程提交网站，网址是https://6824.scripts.mit.edu/2020/handin.py/。

您可以使用您的麻省理工学院证书或通过电子邮件请求API密钥首次登录。登录后将显示您的API密钥(XXX)，可以使用它从控制台上传lab1，如下所示。

cd ~ / 6.824美元
$ echo XXX > api.key
让美元lab1
检查提交网站，确保它认为你提交了这个实验室!

您可以多次提交。我们将使用你上次提交的时间戳来计算逾期天数。
```



```
package main

import (
	"fmt"
	"log"
	"net"
	"net/rpc"
	"sync"
)

//
// Common RPC request/reply definitions
//

const (
	OK       = "OK"
	ErrNoKey = "ErrNoKey"
)

type Err string

type PutArgs struct {
	Key   string
	Value string
}

type PutReply struct {
	Err Err
}

type GetArgs struct {
	Key string
}

type GetReply struct {
	Err   Err
	Value string
}

//
// Client
//

func connect() *rpc.Client {
	client, err := rpc.Dial("tcp", ":1234")
	if err != nil {
		log.Fatal("dialing:", err)
	}
	return client
}

func get(key string) string {
	client := connect()
	args := GetArgs{"subject"}
	reply := GetReply{}
	err := client.Call("KV.Get", &args, &reply)
	if err != nil {
		log.Fatal("error:", err)
	}
	client.Close()
	return reply.Value
}

func put(key string, val string) {
	client := connect()
	args := PutArgs{"subject", "6.824"}
	reply := PutReply{}
	err := client.Call("KV.Put", &args, &reply)
	if err != nil {
		log.Fatal("error:", err)
	}
	client.Close()
}

//
// Server
//

type KV struct {
	mu   sync.Mutex
	data map[string]string
}

func server() {
	kv := new(KV)
	kv.data = map[string]string{}
	rpcs := rpc.NewServer()
	rpcs.Register(kv)
	l, e := net.Listen("tcp", ":1234")
	if e != nil {
		log.Fatal("listen error:", e)
	}
	go func() {
		for {
			conn, err := l.Accept()
			if err == nil {
				go rpcs.ServeConn(conn)
			} else {
				break
			}
		}
		l.Close()
	}()
}

func (kv *KV) Get(args *GetArgs, reply *GetReply) error {
	kv.mu.Lock()
	defer kv.mu.Unlock()

	val, ok := kv.data[args.Key]
	if ok {
		reply.Err = OK
		reply.Value = val
	} else {
		reply.Err = ErrNoKey
		reply.Value = ""
	}
	return nil
}

func (kv *KV) Put(args *PutArgs, reply *PutReply) error {
	kv.mu.Lock()
	defer kv.mu.Unlock()

	kv.data[args.Key] = args.Value
	reply.Err = OK
	return nil
}

//
// main
//

func main() {
	server()

	put("subject", "6.824")
	fmt.Printf("Put(subject, 6.824) done\n")
	fmt.Printf("get(subject) -> %s\n", get("subject"))
}
```

```
package main

import (
	"fmt"
	"sync"
)

//
// Several solutions to the crawler exercise from the Go tutorial
// https://tour.golang.org/concurrency/10
//

//
// Serial crawler
//

func Serial(url string, fetcher Fetcher, fetched map[string]bool) {
	if fetched[url] {
		return
	}
	fetched[url] = true
	urls, err := fetcher.Fetch(url)
	if err != nil {
		return
	}
	for _, u := range urls {
		Serial(u, fetcher, fetched)
	}
	return
}

//
// Concurrent crawler with shared state and Mutex
//

type fetchState struct {
	mu      sync.Mutex
	fetched map[string]bool
}

func ConcurrentMutex(url string, fetcher Fetcher, f *fetchState) {
	f.mu.Lock()
	already := f.fetched[url]
	f.fetched[url] = true
	f.mu.Unlock()

	if already {
		return
	}

	urls, err := fetcher.Fetch(url)
	if err != nil {
		return
	}
	var done sync.WaitGroup
	for _, u := range urls {
		done.Add(1)
        u2 := u
		go func() {
			defer done.Done()
			ConcurrentMutex(u2, fetcher, f)
		}()
		//go func(u string) {
		//	defer done.Done()
		//	ConcurrentMutex(u, fetcher, f)
		//}(u)
	}
	done.Wait()
	return
}

func makeState() *fetchState {
	f := &fetchState{}
	f.fetched = make(map[string]bool)
	return f
}


```

```
//
// Concurrent crawler with channels
//

func worker(url string, ch chan []string, fetcher Fetcher) {
	urls, err := fetcher.Fetch(url)
	if err != nil {
		ch <- []string{}
	} else {
		ch <- urls
	}
}

func master(ch chan []string, fetcher Fetcher) {
	n := 1
	fetched := make(map[string]bool)
	for urls := range ch {
		for _, u := range urls {
			if fetched[u] == false {
				fetched[u] = true
				n += 1
				go worker(u, ch, fetcher)
			}
		}
		n -= 1
		if n == 0 {
			break
		}
	}
}

func ConcurrentChannel(url string, fetcher Fetcher) {
	ch := make(chan []string)
	go func() {
		ch <- []string{url}
	}()
	master(ch, fetcher)
}


```

```
//
// main
//

func main() {
	fmt.Printf("=== Serial===\n")
	Serial("http://golang.org/", fetcher, make(map[string]bool))

	fmt.Printf("=== ConcurrentMutex ===\n")
	ConcurrentMutex("http://golang.org/", fetcher, makeState())

	fmt.Printf("=== ConcurrentChannel ===\n")
	ConcurrentChannel("http://golang.org/", fetcher)
}

//
// Fetcher
//

type Fetcher interface {
	// Fetch returns a slice of URLs found on the page.
	Fetch(url string) (urls []string, err error)
}

// fakeFetcher is Fetcher that returns canned results.
type fakeFetcher map[string]*fakeResult

type fakeResult struct {
	body string
	urls []string
}

func (f fakeFetcher) Fetch(url string) ([]string, error) {
	if res, ok := f[url]; ok {
		fmt.Printf("found:   %s\n", url)
		return res.urls, nil
	}
	fmt.Printf("missing: %s\n", url)
	return nil, fmt.Errorf("not found: %s", url)
}

// fetcher is a populated fakeFetcher.
var fetcher = fakeFetcher{
	"http://golang.org/": &fakeResult{
		"The Go Programming Language",
		[]string{
			"http://golang.org/pkg/",
			"http://golang.org/cmd/",
		},
	},
	"http://golang.org/pkg/": &fakeResult{
		"Packages",
		[]string{
			"http://golang.org/",
			"http://golang.org/cmd/",
			"http://golang.org/pkg/fmt/",
			"http://golang.org/pkg/os/",
		},
	},
	"http://golang.org/pkg/fmt/": &fakeResult{
		"Package fmt",
		[]string{
			"http://golang.org/",
			"http://golang.org/pkg/",
		},
	},
	"http://golang.org/pkg/os/": &fakeResult{
		"Package os",
		[]string{
			"http://golang.org/",
			"http://golang.org/pkg/",
		},
	},
}
```

