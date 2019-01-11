> 前言：之前在多台机器上布置了docker，然后用nginx做反向代理，现在用jmeter对搭载的nginx服务器进行压力测试，看看性能如何。
## jmeter简介
JMeter作为一款广为流传的开源压测产品，最初被设计用于Web应用测试，如今JMeter可以用于测试静态和动态资源，例如静态文件、Java 小服务程序、CGI 脚本、Java 对象、数据库、FTP服务器等等，还能对服务器、网络或对象模拟巨大的负载，通过不同压力类别测试它们的强度和分析整体性能。另外，JMeter能够对应用程序做功能测试和回归测试，通过创建带有断言的脚本来验证你的程序返回了你期望的结果。为了最大限度的灵活性，JMeter允许使用正则表达式创建断言。
JMeter的特点包括对HTTP、FTP服务器、数据库进行压力测试和性能测试；完全的可移植性；完全 Swing和轻量组件支持包；完全多线程；缓存和离线分析/回放测试结果；可链接的取样器；具有提供动态输入到测试的功能；支持脚本编程的取样器等。在设计阶段，JMeter能够充当HTTP PROXY（代理）来记录浏览器的HTTP请求，也可以记录Apache等WebServer的log文件来重现HTTP流量，并在测试运行时以此为依据设置重复次数和并发度（线程数）来进行压测。

## jmeter安装
1.jmeter主程序
windows下载zip格式就好，解压就可以使用，进入bin目录运行jmeter.bat启动jmeter。
下载地址：http://jmeter.apache.org/download_jmeter.cgi

2.java环境
现在最新的版本是jmeter 5.0了，要求java8或者java9的环境。我下载的是java8，下载地址[点击这里](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。上面有各种系统对应的下载文件，如果你的电脑也是windows 64位系统，那么就下载`jdk-8u191-windows-x64.exe`这个文件。
安装完之后，在cmd命令窗口中输入`java -version`，出现你安装的版本信息就成功了。

## jmeter使用
这一部分分界面来介绍jmeter的一些概念，如何使用jmeter读入外部文本，如何根据jmeter产生的结果来生成漂亮的ui报告。
**线程组界面**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190110142626524.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzMzNDU1NDQ3,size_16,color_FFFFFF,t_70)

Number of Threads（users）:开启的线程数，也就是模拟的用户数。例如，设置成500，那么就是发送500个请求。
Ramp-Up Period(in seconds)：在多长时间内建立全部的线程。默认值是0，当为0时， 将立即建立所有线程，假设ramp-up period 设置成T 秒， 全部线程数设置成N个， JMeter 将每隔T/N秒建立一个线程。
Loop Count：每个线程执行的次数，可勾选forever

`Samples(测试样本数) = Number of Threads*Loop Count`
如果像上图那么设置，那么Samples=500*1=500

**HTTP请求页面**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190110165226921.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzMzNDU1NDQ3,size_16,color_FFFFFF,t_70)

在这个页面分为basic和advanced两个子页面，这里只介绍basic页面的一些设置。

Protocol：协议，默认为http
Server Name or IP:这里填服务器名称或者ip地址
Port Number：端口号，一般是8080
Path:路径
Method：请求方法，可以选很多种，不过我们常见就是GET或者POST 
Content encoding:内容编码，如果发送的是中文，务必写上 utf-8

拿百度翻译的api来举例：    https://fanyi.baidu.com/v2transapi
Protocol：https
Server Name or IP:fanyi.baidu.com
Path：/v2transapi
Method:POST

目光向下
Parameters：传递参数的值和名称
Body Data：简单来说就是一个json格式的值
Files Upload：模拟文件上传

如图，比如在这个页面上，需要传递两个参数，一个是name，一个是text，一种方法如上图所示在Parameters中填入参数相应的name和value，是中文的话最好在encode选项上勾选。如果写成Body Data形式，如下：
```
name=your name&text=your text
```
在上图中，可以看到text那一栏中的value，写的是`${text}`，这其实是一个自定义变量，引用的是外部数据，设置自定义变量是在数据配置界面。为什么要使用自定义变量呢？在模拟需要发送数据的请求时，直接用同一个值重复发送500次也是500个样本，但是在真实环境下，用户发送的数据是不一样的，通过配置外部数据，我们可以最大程度的进行模拟真实情况，更好的评测服务器的负载能力。

**数据配置界面**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190110172548900.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzMzNDU1NDQ3,size_16,color_FFFFFF,t_70)
Filename：外部文件所保存的路径，常用的有txt格式和csv格式

File encoding：编码方式，中文的一般填utf-8

Varale Names（Comma-delimited）：定义变量名，供其他模块调用。如果存在多个变量时，用`,`隔开。如上文中的`${text}`，其中的text就是在这里定义的。

Delimiter（use “\t“ for tab）：是指在如果有多个参数时，在定义的这个符号为分割符号，进行提取，表格形式用\t。比如：需要引用的变量为name，password，text。txt中每行的格式为xiaohong;123;test，那么分隔符便为`；`。

Allow quoted data：是否允许引用数据；通常用在文件中有参数值内部包含分隔符的场景。用`""`将包含分隔符的参数括起来，例如设置的Delimiter为`;`text参数值中包含了`;`那么文本中格式便为xiaohong;123;"text;test"。

Recycle on EOF：到数据文件结尾时是否循环读取。设置为True时，线程数过多，数据文件读取到最后一行时，会再次从第一行开始读取。设置为False，到达文件结尾时如继续读取，则值会默认为<EOF>，可通过设置jmeter属性csvdataset.eofstring来改变该值。

Stop thread on EOF：Recycle on EOF设置为False，Stop thread on EOF设置为True，则读取数据文件最后一行后，停止测试，不管还有多少线程组未执行。

Sharing mode：共享模式。默认在所有线程组中使用，可选择每个线程组单独打开

**HTTP头管理页面**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019011017262979.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzMzNDU1NDQ3,size_16,color_FFFFFF,t_70)
该模块用于定制Sampler发出的HTTP请求的请求头的内容。不同的浏览器发出的HTTP请求具有不同的Agent，访问某些有防盗链的页面时需要正确的Reference这些情况下都需要通过HTTP Header Manager来保证发送的HTTP请求是正确的。

**聚合报告界面**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190110172131270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzMzNDU1NDQ3,size_16,color_FFFFFF,t_70)
这个模块用来显示宏观测试结果，也可以将结果数据写入到文件中。对应的指标含义如下：
Label：取样器名称（或者是事务名）

#Samples：取样器运行次数（提交了多少个事务）

Average：请求（事务）的平均响应时间，单位为毫秒。

Min：请求的最小响应时间，单位为毫秒。

Max：请求的最大响应时间，单位为毫秒。

Std.Dev：响应时间的标准方差。

Error%：事务错误率。

Throughput：吞吐率（TPS）。默认情况下表示每秒完成的请求数（Request per Second）

KB/sec：每秒数据包流量，单位是KB。

Avg.Bytes：平均数据流量，单位是Byte。

**结果树界面**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190110172156256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzMzNDU1NDQ3,size_16,color_FFFFFF,t_70)
这个模块用来显示微观测试结果，可以查看每个事务的详细数据，如request的信息，和响应数据。观察这个模块，在事务出错的时候，可以帮助我们了解错误原因，是40x还是50x，从而进行问题的解决。

**后置处理器界面**
beanshell postprocessor
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190110172833459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzMzNDU1NDQ3,size_16,color_FFFFFF,t_70)
在这个页面可以编写Script，来操作请求返回后的结果。如上图所示，我们请求的页面返回了JSON数据，其中包含了中文，如果不进行处理，那么在view result tree界面中观察response data会出现乱码，添加如下代码，即可显示正常。

```
prev.setDataEncoding("utf-8")
```


**生成漂亮的ui报告**
很简单，定位到jmeter bin文件夹下，执行以下命令
```
jmeter -n -t D:\Users\Desktop\Test Plan.jmx -l D:\Users\Desktop\html.csv -e -o D:\Users\Desktop\HttpReports
```
三个路径依次是：xxx.jmx文件路径，输出xxx.csv文件路径，要保存的http report的文件路径。

参考文献：
- https://segmentfault.com/a/1190000007922515
- https://www.cnblogs.com/st-leslie/p/5185376.html
- http://www.51testing.com/zhuanti/jmeter.html
- https://blog.csdn.net/u011541946/article/details/71126799
- https://blog.csdn.net/qq_35885203/article/details/53760285 
- https://www.cnblogs.com/Abby123/p/6651263.html
