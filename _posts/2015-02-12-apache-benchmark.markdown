---
layout: post
title:  "Apache Benchmark简介"
category: system
tags: [ab]
---

ab是Apache自带的HTTP服务器[benchmark工具][1]，并行模拟大规模网络请求，做压力测试用的神器。

ab的请求就类似于[CC攻击][2]，SRE对这类请求做了[防范][3]。试试各大搜索和门户，ab都不会有什么返回的。所以[这篇blog][4]例子就过气了，2009年G还这么傻萌啊。自己看console输出吧，很清楚。

## 普通请求
{% highlight javascript %}
ab -n 100 -c 100 http://www.kwx.gd/
{% endhighlight %}
    *-n requests*     
        请求总数目
    *-c concurrency*  
        并发数

## 高级请求
{% highlight javascript %}
ab -c 50 -k -n 400 -T "Content-Type:text/xml" -p post  http://www.kwx.gd/
{% endhighlight %}
    *-p post*
        post是文件名，里面是post给www.xxx.com的内容，如x=wode或者一段xml信息
    *-T content-type*
        post/put都要指定Content-Type，例如application/x-www-form-urlencoded. Default: text/plain
为什么又是这个站呢？因为我也找不到别的雷锋给你玩儿啊。

[1]: http://httpd.apache.org/docs/2.2/programs/ab.html
[2]: http://netsecurity.51cto.com/art/201306/400210.htm
[3]: http://www.williamlong.info/archives/3181.html
[4]: http://5iwww.blog.51cto.com/856039/226457
