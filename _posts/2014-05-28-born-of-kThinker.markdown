---
layout: post
title: "命名日"
category: other
---

###1. Jekyll 模板
本博使用[Jekyll][9]，文档易读，搭博客绝对的平民技术。找模板走[这里][17]、 [这里][18]和[这里][8]，模板的好处就是靠谱；坏处显而易见，满大街的撞衫。

>**kTer:** Jekyll是什么? Jekyll源码走[这里][10]，就是个Ruby写的静态网页生成器。[Github Pages][11]的网页引擎就是它，天生亲和。Jekyll Philosopyh: Put simply, Jekyll gets out of your way and allows you to concentrate on what truly matters: your content。kTer1："用模板吧，only creation matters"。kTer2："自己写嘛，you have taste"。于是，kTer2被kTer1打死了。

###2. 上传到Github Pages
[Github Pages][11]，文档详细。[室友][7]的页面放在了国内的开源社区[GitCafe][12]，好像也不错。注意，_config.yml中的url项原为'http://localhost:4000'，需要将其设为空。否则在上传后，还会使用路径'http://localhost:4000\\*'来加载css等文件，页面渲染会失败。

###3. 购买域名
本博域名从[阿里云][1]买的，价钱要比[GoDaddy][2]便宜。知呼上有讨论.net、.com、.cn的[选择问题][6]，可供一阅。域名的初衷是按用途分配，但这是不可能，跟保持男寝规整难度一样大。在下不介意从国外服务商处购买，也不介意将页面放在国内。

>**kTer:**卖域名的钱给谁了，为何售价不同？互联网域名的管理者是[ICANN][3]，组织结构走[这里][4]，组织历史走[这里][5]，著名管协议的ITEF等组织都隶属于它。ICANN在上个世纪末接过美国政府的棒来管理互联网。ICANN有域名定价权，各个域名服务商从这里进货后分发销售，另外，ICANN是非盈利机构。服务商会提供附加服务，因此售价不同。下午接到阿里云的电话问要不要服务器，以前买过GoDaddy的域名，净收推广邮件了。

###4. DNS设置
阿里云提供免费的DNS解析，也可以使用[DNSPod][13]。我两个都试过，感觉不出高低。Github Pages的域名自定义设置走[这里][14]，出现问题时走[这里][15]。使用域名还是子域名可能会有点儿tricky，反正我的A类记录没有生效，最后用了www.的子域名。

>**kTer:** Github Pages域名自定义怎么工作的？对于A类记录，指定Github的两个IP，会有更新，要以官方文档为准。这个IP不是工程username.github.io的所在IP，仅仅是跳转服务器。只用两台服务器负载所有的Github Pages是不可能的，username.github.io的IP会不断变化，文档也指出页面是放在CDN上的。如果CNAME和工程的对应关系是跳转服务器集中管理的，那就没什么了。如果这个关系是由内容节点管理的，那么当上传一个CNAME文件后，可能会导致工程的迁移。当没CNAME文件时，工程按工程名Hash部署到内容节点；若有CNAME文件，则按自定义域名Hash选择节点。一个DNS解析发到Github跳转服务器后，会根据CNAME或是工程名进行Hash，O(1)就能定位网页的位置。
本故事纯属梦话，如有雷同，are you kidding?

###5. 评论系统与流量统计
用的[多说][16]，在_layouts\post.html的版权声明上插入多说的代码就ok。评论会被传到多说上，与博客是分离的。访问记录用的是[Google Analytics][19]，可怕的谷歌。
 
[1]: http://www.net.cn
[2]: http://www.godaddy.com
[3]: https://www.icann.org/
[4]: https://www.icann.org/zh/about/learning/beginners-guides/participating-08nov13-zh
[5]: http://zh.wikipedia.org/wiki/%E5%9F%9F%E5%90%8D
[6]: http://www.zhihu.com/question/19751843
[7]: http://oilbeater.com/%E5%8D%9A%E5%AE%A2%E7%9B%B8%E5%85%B3/2014/05/06/blog-migration.html
[8]: http://www.zhanxin.info/themes.html
[9]: http://jekyllrb.com/
[10]: https://github.com/jekyll/jekyll
[11]: https://pages.github.com/
[12]: https://gitcafe.com/
[13]: https://www.dnspod.cn/Domain
[14]: https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages
[15]: https://help.github.com/articles/my-custom-domain-isn-t-working
[16]: https://duoshuo.com
[17]: http://jekyllthemes.org/
[18]: http://yuanyong.org/blog/collect-jekyll-theme.html
[19]: http://www.google.com/analytics/
