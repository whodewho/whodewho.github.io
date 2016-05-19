---
layout: post
title:  "web.py+SAE遇到的坑"
category: programming
tags: [python, jquery, sae, webpy]
---

## 在webpy里使用jquery，$需转义
web.py使用'\$'在模版html里使用python语法，以及读取传入的变量。如果要使用'\$'这个符号本身，可以用'\$\$'进行转义。jquery就用'\$'取selector，当在web.py中使用jquery时，jquery的语句都要改成'\$\$'，或者将'\$'替换成'jQuery'。否则的话，最基本的jquery语句都无法正常运行。

## Bootstrap File Input
Twitter Bootstrap是个很好的前端框架，但是它的文件选择器一直没有做好，[Twitter Bootstrap File Input][1]是个很好的补丁，但就算是把js和css照搬下来也出不来它的效果，学生时就进过这坑，很久。解决方法就是把bootstrap.file-input.js里的这行注了，相当于`this.each(function(i,elem){`不运行，Than it works。
另一个[补丁][2]效果不错的样子，但太复杂我不需要

{% highlight javascript %}
$elem.wrap('<a class="file-input-wrapper btn btn-default ' + className + '"></a>').parent().prepend($('<span></span>').html(buttonWord));
{% endhighlight %}

## web.py多文件上传
传一个文件是很简单的，有时候是不定个数的文件要上传，这时`file input`需要共用一个`name`，在后端接收一个数组，依稀记得php就是用`name[]`的命名方式实现

webpy有专门的`web.input`API来接收用户输入，但是多文件上传doc里没有讲

我的代码如下，其中pic就是图片输入，des对应pic，这俩是不定个数的。pageTitle就是页面标题，只有一个。注意数组和字典的差别，pic和des里只有文件内容或文本输入的内容，但是pageTitle这个接收里，就有输入的描述信息，如大小等等。可以参考[这里][9]和另一个链接介绍`{}`和`[]`的区别但是我就是就是找不到了，自己print吧。
{% highlight python %}
x = web.input(pic=[])
y = web.input(des=[])
t = web.input(pageTitle={})

i = 0
for p, d in zip(x['pic'], y['des']):
    if len(p)==0:
        continue
    bucket.put_object(dateId+"/"+tupleId+"/"+str(i)+'.jpg', p)
    data['des'].append(d)
    i = i + 1
{% endhighlight %}

## js上传图片预览、前端图片压缩
请参考这个[例子][3]

图片预览的实现就是将上传的文件赋给一个`<img>`标签的`src`属性

前端图片压缩，就是使用canvas标签，将上传的图片绘制到canvas上，改变canvas的大小，然后将canvas的值赋给一个`hidden input`，将原来的`file input` disable掉，这样以来，提交的就是压缩过的图片。但是，这种压缩不保证都ok。(为什么我没有遇到，但用户好多都遇到了..)
贴代码吧，jquery取相邻、父亲、孩子标签的能力简直太棒了
{% highlight python %}
$$( document ).ready(function() {
            $$( ".imageLoader").change(function() {
                var img = ($$(this).closest("a").siblings("#thumbnail")[0])
                img.src = window.URL.createObjectURL(this.files[0]);

                var imageCanvas = document.createElement("canvas");
                var ctx = imageCanvas.getContext("2d");

                var imageHidden =  ($$(this).closest("a").siblings(".imageHidden")[0]);

                $$(this).closest("a").siblings(".afterbrowse").show();

                img.onload=function(){
                    var MAX_WIDTH = 800;
                    var MAX_HEIGHT = 600;
                    var width = img.width;
                    var height = img.height;
                     
                    if (width > height) {
                      if (width > MAX_WIDTH) {
                        height *= MAX_WIDTH / width;
                        width = MAX_WIDTH;
                      }
                    } else {
                      if (height > MAX_HEIGHT) {
                        width *= MAX_HEIGHT / height;
                        height = MAX_HEIGHT;
                      }
                    }
                    
                    imageCanvas.width = width;
                    imageCanvas.height = height;

                    ctx.drawImage(img, 0, 0, width, height);
                    imageHidden.value=imageCanvas.toDataURL("image/jpeg");
                    appendItem();
                }
            });
{% endhighlight %}

## SAE和BAE的Storage
云服务里，app是没有权限创建文件、目录的，上出的文件都是存在云存储里的，想想我欢快的`mkdir `也是醉了。

因为图片前端压缩会损坏图片，我尝试在SAE里不压缩图片，直接上传，但是SAE的nigix后台对上传的文件大小做了限制，出现`413 request entity too large`这种错误，2M的图片也刁难，如之奈何。于是就有了BAE的一番蹉跎。

我用SAE的[Python Storage][4]时，我觉得这文档简直弱爆了，当我试用BAE的[Python Storage][5]时，我觉得SAE简直太好了。BAE里[例子][6]过气这种事儿是伤人心的，比着[SDK文档][7]猜API怎么用我也OK，但我两次用BAE，本地调试环境都望而却步，边push边debug这种事儿，我是再也不想干了。最终也没解决为什么text能写到BCS里，但image就不行的问题。

最终还是留了个带压缩功能的[连图][8]，连图的代码在[这里][10]

今天入职小米了，海伯利安里有这样一句，“他的锋芒掩盖了痛苦”

少年人，我为你祝福。

[1]: http://gregpike.net/demos/bootstrap-file-input/demo.html
[2]: https://blueimp.github.io/jQuery-File-Upload/basic.html
[3]: http://hacks.mozilla.org/2011/01/how-to-develop-a-html5-image-uploader/
[4]: http://sae.sina.com.cn/doc/python/storage.html
[5]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/bcs#bucket
[6]: http://pythondoc.duapp.com/bcs.html
[7]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/bcs/sdk
[8]: http://liantu.kthinker.com/default/t/jkYFEAyTLS
[9]: https://groups.google.com/forum/#!topic/webpy/2k3x6ULb5t8
[10]: https://github.com/whodewho/photoline