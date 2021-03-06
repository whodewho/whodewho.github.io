---
layout: post
title:  "测试与纳税"
category: test
---

本文译自Google Testing Blog: [Cost of Testing][1] 
在此向被我bug折磨过的老师说：我本该写单元测试的，可惜，你来早了。

...
说白了，写单元测试就是给代码交10%的税，纳税是有回报的，如下：

* 当开发一个简单的feature时，不再需要把整个软件转起来，登录、验证、跳转，一步步的点到那个页面去确定这个feature正常与否。意味着不用刷新网页、手动输入数据来验证功能是否正常，单元测试过就OK。能省出大把的时间来发癔症。
* Regression几乎为零。当你在A加feature时，你可能无意间破坏了B模块的约束。每天至少有那么一回，我的修改会让B模块的测试用例挂掉。自己都吓一跳，怎么可能？！这些红灯能节省很多时间，别码完了才发现，根本连不起来啊！B模块的代码早变了，要是你这时你还忘了B怎么实现的...(相信我，你肯定忘了)
* 可以节省脑力缓存，不必记住这个功能怎么实现的，假设是什么，输入是什么。测试用例就是最靠谱的文档，清清楚楚的。开多久的会，放多久的假回来，你的代码还是你的代码。
* 可以更方便的重构。代码不翻晒，早晚发霉，别说别人看不懂，你自己可能都看不懂。尤其是大项目，大家碰都不愿碰，几乎是重新写一遍的代价。(热力学第一定律：别人写的都是翔)要是单元测试靠谱，就更容易把代码捡起来，重构的代价也更小。

写测试代码，不仅愉快当下，也惠及以后。虽然写单元测试要花去开发时间的10%，但回报是远大于10%的。就算不考虑长期的好处，短期内也是很有利的，我TDD的开发速度比正常的要快。具体快多少要看项目的复杂程度，项目越复杂，逻辑越多，测写结合的效果越明显。

想像下被问到TDD的代价到底有多大时，我心中奔腾的感叹号吧！

（记住，口号都是XIE教）

[1]: http://googletesting.blogspot.hk/2009/10/cost-of-testing.html