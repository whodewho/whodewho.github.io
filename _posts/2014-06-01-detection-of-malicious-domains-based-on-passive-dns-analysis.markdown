---
layout: post
title: 基于Passive DNS的恶意域名识别研究
category: research
tags: [ml, python]
---

本文是我的硕士毕业设计，目测以后也用不到了，望能帮到后来者。情真必然言简，本文只是PPT的要点，外链请自行递归，nothing more。

-----

## 背景
恶意域名指传播蠕虫、病毒和特洛伊木马或是进行诈骗、色情内容传播等不法行为的网站域名。本文面临能够的挑战，就是恶意网站经营者所使用的各种技术。近年来，FFSN和DGA技术使得网络攻击更加隐蔽，隐患更难被清除。

* Fast-Flux

![FFSN](http://www.honeynet.org/sites/default/files/files/images/web-diagram.gif)
如图所示，左边是合法网站的用户请求过程，客户机直接从服务器取回网页内容。右边是FFSN的请求过程，客户机仅与代理机器进行交互，代理机器从后台服务器取回内容返回给客户机。代理机器就是被感染控制的PC机器，它们往往属于一个僵尸网络，称为僵尸节点，这些节点被mothership控制，恶意域名如www.example.com的DNS记录仅指向代理节点，代理节点很多，形成一个防护层，使得恶意网站服务器难以被追踪。=》[详情][2]

* DGA

<img src="/images/dga.jpg" alt="DGA">

DGA技术是指通过算法自动生成域名，僵尸网络的内部通信就使用了这种技术。僵尸节点与控制服务器也通过DNS解析建立通信，因为安全管理，恶意域名会被打击失效，控制服务器会迁移到新的域名上，僵尸节点在连接失败后，会通过约定的DGA算法不断的尝试(动画)，最终找到后台服务器。DGA算法难以逆向，网络管理者也无法阻止整个域名池，难以防范。=》[详情][3]

一个重要的研究方向是分析域名的DNS数据来识别域名。根据DNS数据的来源不同可分为主动测量和被动测量两种，主动测量就是伪装成不法网站的访问者，不断发起DNS请求来获取FFSN的信息；被动测量指使用Passive DNS技术，是一个比较热门的研究方向

* Passive DNS

Passive DNS是DNS历史信息的一个截片，是DNS数据的一种应用方式，简单的Passive DNS的例子如图所示，是域名和IP的映射表。Passive DNS能够回答的问题包括，域名指向哪些地址，域名服务器管理过哪些域名等等。

<img src="/images/pdns.png" alt="pdns"> 

-----
恶意域名和合法域名的DNS行为有着很大的差异，而这种差异能通过Passive DNS数据体现出来。恶意域名的特点有很多，如DNS记录TTL值偏小、IP池更新快、IP网段丰富等等。
目前的使用Passive DNS比较优秀的工作有EXPOSURE、Notos和FluxBuster等等。


## 操作流程
本文的操作流程如下图所示：

<img src="/images/approach.jpg" alt="pdns" > 

###1. **DNS中间日志**
网络流量转化到DNS中间日志用到了gamelinux的[passivedns][4]工程。

###2. **存储模型**
生成中间日志后，需要选则数据库和存储模型来存放Passive DNS数据进行分析。本文使用过关系数据库，但发现当数据达到千万量级后，查询的效率会非常的低，即使进行优化无法避免表遍历和空间浪费等问题。目光投向NoSQL数据库，最终本文选择了TokuMX。

存储模型如例，域名和IP被当作主键(动画)分别存储到不同的Collection中，Collection相当于关系库中的表。域名主键仅保留2LD，如www.163.com以163.com主键，域名条目包含的IP、计数、TTL、子域名等信息。

<img src="/images/storage-model.jpg" alt="storage-model">

* 为什么将域名和IP分别存储？
一次查询得到域名的全部信息，无需遍历
一定程度的冗余，IP信息也有意义
* 为什么只取了域名的尾部？
www.pku.edu.cn, 2LD为pku.edu.cn
关注域名主体，有聚类效果
* 损失了细节，是否有负面影响？
单个IP的详细信息意义对域名没有影响
* 为什么按时间切分数据？
观察域名随时间的演化
MongoDB文件块大小限制为16M，单个条目不能无限增长

###3. **数据去冗、降噪**
* 在数据挖掘前需要对数据进行预处理，本文首先会滤去TTL值过大、IP集合数过小、IP网段过于单一的域名，这类域名不可能是FFSN域名，但它们占了很大比例
* 另一个重要的干扰是CDN域名，FFSN被称为非法的CDN，CDN有很多特性类似于FFSN域名，CDN节点遍布世界各地、网段丰富。本文滤除全球著名的50个CDN域名，如akamai，以及使用这些CDN的IP的域名，然而这种滤除只是初步的，滤除时使用的参考IP只是已知的CDN服务商的域名IP，不代表这些服务商所有的IP，无法保证所有使用CDN的域名被滤除。
* 另一类噪音数据是通过观察IP得到的，这类IP的域名数量极多，域名的子域名、主体部分和TLD串长度的熵值都很高，表明IP的域名形式非常丰富、复杂，观察后发现大都是待售域名。

###4. **特征抽取**
本文的特征模型如图，粗体字特征为本文首次提出。

<img src="/images/feature.jpg" alt="feature"> 

在现实数据中，恶意域名和合法域名的**特征值分布差异是很明显的**，图可以在我论文里找到。

###5. **算法与实验**
本文的算法用的scikit-learn的随机森林。

* **标识**
合法域名用的Alexa里排名较高的域名。恶意域名标识用的Zeus，SpyEye，Palveo，Feodo的域名，这是四个botnet。但这部分域名数目过少，本文还添加了一些普通的恶意域名阻止列表的域名，如CyberCrime、Malware Domain List、Malware Domains、Phish Tank、hpHosts。
准确的讲，这样做训练是不靠谱的。但是，Fast-Flux域名生命期短、更新快，恶意域名标识过少困扰着这类研究。可以粉饰为模型也适用于普通恶意域名，但多少有点儿迫不得已的味道。

* **实验结果**
跑了半个多月的数据，如果好好Tunning普通域名的比例的话，每天都大体如此。图中测试数据合法、恶意域名的比例约8:1，合法域名450左右。

<img src="/images/confusion.jpg" alt="confusion"> 

* **特征性能**
深深的怨念，恶意标识不可靠，使得增长性特征表现的如此之逊。最强的还是IP数目和IP网段多样性这俩古老的特征。

<img src="/images/importance.jpg" alt="importance"> 

* **无标识检验**
在无标识的数据上也发现了一些恶意域名如extabit.com、2iij.net、1818nic.com、cdnok.com、critsend.com、80s.la、cloud-service.com...其中critsend.com是一个垃圾邮件服务。

### 6. **展望**
* 通过滑动窗口来切分数据，细化数据周期，提高识别实时性
* 恶意标示少且不可靠的问题可能是无解的，用无监督学习试试


------
其它需要可查看[源码][1]和[论文][5]，Good Luck。

[1]: https://github.com/whodewho/FluxEnder
[2]: http://www.honeynet.org/papers/ff/
[3]: http://nbviewer.ipython.org/github/ClickSecurity/data_hacking/blob/master/dga_detection/DGA_Domain_Detection.ipynb
[4]: https://github.com/gamelinux/passivedns
[5]: http://lib.pku.edu.cn
