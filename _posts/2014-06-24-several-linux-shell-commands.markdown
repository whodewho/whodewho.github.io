---
layout: post
title:  "几个Linux命令"
category: system
tags: [linux]
---

##1.北大网关
一旦打不开网页，首先就是把网关重新连一下，这个动作的频率极高
ipgwclient gui版的容易卡死，占内存;网页版吃流量，不记密码，有太多次重输经历，尤其是在手机上。但我竟然忍了那么久，安于现状是很S13的..
来个nogui版的吧，在.bashrc里设置alias命令

```
alias ipg='ipgwclient connect 用户名 密码 global'
```

操作过程，感觉很快了

{% highlight c++ %}
ctrl+alt+t
ipg
alt+f4
{% endhighlight %}

还想更快？
当半个小时(瞎蒙的)没有流量时，网关就是会把用户断掉(这是真的)。客户端和网关间是有心跳信号的，如果能够捕捉到这个中断，就可以达到一直在线。循环连接就不说了。


##2.杀死一个Linux进程

```
killall -9 python
```

或者(多余)，写一个kill.sh文件

{% highlight c++ %}
kill -9 $(ps aux | grep $1 | awk '{print $2}')
{% endhighlight %}


修改~/.bashrc，将文件路径加入到PATH里，运行方法如下

```
sh kill.sh python
```

有趣的是，在写博客前，我用的都是'多余'的方法，unknown unknown，详情看[这里][1]

##3.修改文件编码
单个修改，原地覆盖

```
iconv -f gbk -t UTF-8 tmp.txt -o tmp.txt
```

一夹的乱码文件，用[find][3]批量处理吧

```
find . -name "*.tex" -exec iconv -f gbk -t UTF-8 {} -o {} \;
```

MS系文件转linux，dos2unix戳[这里][2]

```
find . -name "*.tex" -exec dos2unix {} \;
```

##4.压缩与解压
这个用的很多，但我每次用得查一下，man是你的好朋友，但它太罗嗦了

```
tar -czvf 文档名.tgz 文件名 [目录]...
tar -xzvf 文档名.tgz [-C 目录]
```

google 'linux tar'的结果一直再变，记得以前出的每一个网页特别简陋，但就这两句话，也说清楚了，现在[这个][4]还不错



[1]: http://vbird.dic.ksu.edu.tw/linux_basic/0440processcontrol.php#kill
[2]: http://vbird.dic.ksu.edu.tw/linux_basic/0310vi.php#dos2unix
[3]: http://vbird.dic.ksu.edu.tw/linux_basic/0220filemanager.php#find
[4]: http://www.vixual.net/blog/archives/127




