# 网络优化  

## 一、网络优化的必要性  

### （1）流量
App的流量消耗对用户来说是比较敏感的, 虽然最近开始流量比较便宜，很多人开始使用无线网，但是大部分用户在室外没有wifi的时候还是比较关心流量的消耗。尤其是相对于网络不发达地区的用户，更需要减少不必要的网络消耗。  

### （2）用户体验  
关注网络异常情况进行优化可以让app有更好的用户体验。如果页面加载时间过长，会流失很多第一次使用app的用户。就我个人而言，如果一个页面加载时间太长，如果对某个内容比较感兴趣会等待一会，如果很久没有响应，那么就没有了进一步了解的兴趣。  

### （3）电量消耗
电量优化也是Android优化的重要内容之一，网络请求是消耗电量的，多余的网络请求无疑是消耗了额外的电量。  

## 二、如何进行网络优化  

### （1）减少网络的请求频次
注册登录。 正常会有两个API, 注册和登录, 但是设计API时我们应该给注册接口包含一个隐式的登录. 来避免App在注册后还得请求一次登录接口(有可能失败, 从而
导致业务流程失败)。对这个了解的不多，主要的工作应该还是在Server端。目前我们的app是两个接口分开的。  

###  (2)压缩传输数据
使用Gzip来压缩request和response, 减少传输数据量, 从而减少流量消耗。Gzip在压缩的时候核心思想是：如果一个串中有两个重复的串，那么只需要知道第一个
串的内容和后面串相对于第一个串起始位置的距离 + 串的长度。  

举个栗子：
假如有 ABCDEFGABCDEFH → ABCDEFG(7,6)H。7 指的是往前第 7 个数开始，6 指的是重复串的长度，ABCDEFG(7,6)H 完全可以表示前面的串，并且是没有二义性的。  
Gzip工作流程如下图
![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/gzip_pic.png)  
                                                （此图片来自网络）  
首先,客户端发请求给服务端,会带上请求头:Accept-Encoding:gzip。第二步，服务端接收到请求头后，可以选择压缩或不压缩。第三步，服务端选择压缩后，文件明
显变小，同时在响应头加上Content-Encoding:gzip。第四步，客户端接收到响应后，根据响应头中是否带有Content-Encoding:gzip，判断文件是否被压缩，如果压缩就进行解压，如果没有压缩，就按照正常方式读取数据即可。  

![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/fildder.png)    

这是用fiddler抓取的我们的一个接口的请求，可以看到header里面有Accept-Encoding: gzip，代表okhttp开启了Gzip压缩。okhttp默认开启gzip压缩。  

### (3)动态获取图片大小、质量
比如天猫，在网络较好或者wifi的情况下获取高质量图片，在网络较差的情况下(3G,4G）获取低质量图片。关于这个我们项目暂时无法实施。  

### (4)打包网络请求
比如学习记录一条一条上传肯定不如一次上传多条更节省流量，header里面的一些内容都是重复的，这部分资源就节省下来了。  

### (5)选择性提前加载数据
判断当前用户的网络条件，假如是WIFI的情况下，可以适当提前加载一些分页的数据等等，这样既不消耗用户数据流量，在加载下一部分数据时，因为提前加载，用户视觉上会显得更流畅，建议在用户使用频率较高的页面（如果有做行为采集，可以和相关人员确认，并统计这些页面）和数据量较大的接口做这个优化。  

### (6)实现网络缓存
网络缓存可以在网络较差无响应或者页面加载失败的时候加载缓存数据，让用户在离线的情况下也可以使用部分app的功能。HTTP本身提供了一套缓存相关的机制。这套机制定义了相关的字段和规则，用来客户端和服务端进行缓存相关的协商，如响应的数据是否需要缓存，缓存有效期，缓存是否有效，服务器端给出指示，而客户端则根据服务端的指示做具体的缓存更新和读取缓存工作。    

http缓存可以分为两类：  

强制缓存:是直接向缓存数据库请求数据，如果找到了对应的缓存数据，并且是有效的，就直接返回缓存数据。如果没有找到或失效了，则向服务器请求数据，返回数据和缓存规则，同时将数据和缓存规则保存到缓存数据库中。   

对比缓存：是先向缓存数据库获取缓存数据的标识，然后用该标识去服务器请求该标识对应的数据是否失效，如果没有失效，服务器会返回304未失效响应，则客户端使用该标识对应的缓存。如果失效了，服务器会返回最新的数据和缓存规则，客户端使用返回的最新数据，同时将数据和缓存规则保存到缓存数据库中。

Okhttp可以添加缓存目录并实现缓存，但仅限于GET请求，理论上POST请求也是可以实现的，官方给的说法是需要考虑的情况比较多，不是很有必要。这是我找的一个使用了retrofit2+okhttp3+rxjava作缓存的项目。主要思想就是自定义网络拦截器，根据自定义的注解决定是否，hook网络请求，可以参考下具体的实现。  

项目地址：https://github.com/yale8848/RetrofitCache

关于OKHTTP网络缓存的一些资料：  
https://blog.csdn.net/polo2044/article/details/80779528  
https://blog.csdn.net/polo2044/article/details/82228886  
https://imweb.io/topic/5795dcb6fb312541492eda8c

我看了下我们app好像不是很有必要做一些网络缓存，如果有必要就定一下方案，实现这个不难，主要是需要大量时间测试。

## 三、进行网络监测  

### 一些网络监测的可视化的工具  

#### (1)AndroidStudio自带的可视化工具 Network Monitor  

![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/network_monitor.png)  

这是3.1.3 版本AndroidStudio Network Monitor的截图，不同版本的界面不一样，打开相应的页面点击网络请求就可以看到发送合接收的数据了，以及网络状态等，这个比较简单，就不展开讲了。  

#### (2)fiddler
这个我们用的比较多，下图显示我们接口请求的发送和接收的数据  

![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/fiddler_1.png)

其他还可以使用的工具也有，比如Charles，wireShark等等，这两个基本没用过，wireShark以前网络课学习tcp三次握手使用过，功能比较强大，但是用起来也麻烦。  
#### (3)弱网下的app的使用情况(有几个图截图老是失败，用手机拍的比较模糊)

这个是我之前比较忽略的点，一般测试比较多的是无网或者网络错误情况下的界面UI的展示。现在我们默认的请求超时时间是60秒。这次主要介绍的还是fiddler,在工具栏中找到Rules，再到Rules列表中找到Customize Rules  

![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/fiddler_3.png)    

这个时候会弹出一个类似于文本编辑器的东西,在这个文本编辑器中使用Ctrl+F使用搜索功能搜索关键字：simulate，找到下图的代码段：  

![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/fiddler_4.png)    

request-trickle-delay代表的是你网络请求的延迟时间，response-trickle-delay代表的是网络响应的延迟时间，单位都是毫秒，这里默认给的是300毫秒和150毫秒，修改这2个值即可模拟网络延迟和弱网络环境：  

![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/fiddler_5.png)  

接下来开启网络延迟，Rules功能中，找到Performance，然后在子选项中可以看到一个Simulate Modems Speeds，选中开启网络延迟，如果需要关闭网络延迟，再次点击即可。如下图显示： 

![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/fiddler_6.png) 

### 自己的设想和实现  
我自己的设想是做一个小的工具进行使用，现在做了一个比较粗糙的版本。迭代开发之后可以开启此功能对修改的页面进行监控，app在前台时网络请求消耗是否有异常，在后台时是否有不必要的网络请求在后台运行，再对相关的代码进行定位。

项目地址：https://github.com/weihuihuang/TrafficMonitor   

下面是第一个版本在前后台的部分效果图  

![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/screenshot_1.jpg)    

![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/screenshot_2.jpg)    

![image](https://github.com/weihuihuang/MyAndroidStudyNotes/blob/master/pic/networkpics/screenshot_3.jpg)   







