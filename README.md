# 网络优化  

## 网络优化的必要性  

### 流量
App的流量消耗对用户来说是比较敏感的, 虽然最近开始流量比较便宜，很多人开始使用无线网，但是大部分用户在室外没有wifi的时候还是比较关心流量的消耗。  
尤其是相对于网络不发达地区的用户，更需要减少不必要的网络消耗。
### 用户体验  
关注网络异常情况进行优化可以让app有更好的用户体验。如果页面加载时间过长，会流失很多第一次使用app的用户。就我个人而言，如果一个页面加载时间太长，  
如果对某个内容比较感兴趣会等待一会，如果很久没有响应，那么就没有了进一步了解的兴趣。
### 电量消耗
电量优化也是Android优化的重要内容之一，网络请求是消耗电量的，多余的网络请求无疑是消耗了额外的电量。  

## 如何进行网络优化  

### 减少网络的请求频次
注册登录。 正常会有两个API, 注册和登录, 但是设计API时我们应该给注册接口包含一个隐式的登录. 来避免App在注册后还得请求一次登录接口(有可能失败, 从
而导致业务流程失败)。对这个了解的不多，主要的工作应该还是在Server端。目前我们的app是两个接口分开的。  

### 减少网络的请求频次
使用Gzip来压缩request和response, 减少传输数据量, 从而减少流量消耗。Gzip在压缩的时候核心思想是：如果一个串中有两个重复的串，那么只需要知道第一个
串的内容和后面串相对于第一个串起始位置的距离 + 串的长度。
举个栗子：
假如有 ABCDEFGABCDEFH → ABCDEFG(7,6)H。7 指的是往前第 7 个数开始，6 指的是重复串的长度，ABCDEFG(7,6)H 完全可以表示前面的串，并且是没有二义性的。
