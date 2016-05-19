---
layout: post
title:  "如何使用Jekyll的Category和Tag"
category: other
tags: [jekyll]
---

文章都有分类和标签的需要，如上面。本文介绍如何使用Jekyll的Category和Tag功能，参考了[minddust][1]的文章，对不work的地方进行了更新和补充


1.在post.html layout里添加下面代码，读取文章的Category和Tag，生成展示用的HTML。执行jekyll serve会运行这些liquid代码，实时修改代码时，jekyll会自动执行。minddust此处用的是post.tags，这个变量经我测试是不识别的，改为page.tags的话work

{% highlight liquid %}
{% raw %}
{% if page.tags.size > 0 %}
    {% capture tags_content %}Posted with {% if page.tags.size == 1 %}<i class="fa fa-tag"></i>{% else %}<i class="fa fa-tags"></i>{% endif %}: {% endcapture %}
    {% for post_tag in page.tags %}
        {% for data_tag in site.data.tags %}
            {% if data_tag.slug == post_tag %}
                {% assign tag = data_tag %}
            {% endif %}
        {% endfor %}
        {% if tag %}
            {% capture tags_content_temp %}{{ tags_content }}<a href="/blog/tag/{{ tag.slug }}/">{{ tag.name }}</a>{% if forloop.last == false %}, {% endif %}{% endcapture %}
            {% assign tags_content = tags_content_temp %}
        {% endif %}
    {% endfor %}
{% else %}
    {% assign tags_content = '' %}
{% endif %}
{% endraw %}
{% endhighlight %}

2.将下面代码放入post.html layout里你喜欢的位置，展示1生成的HTML内容

{% highlight liquid %}
{% raw %}
<p id="post-meta">{{ tags_content }}</p>
{% endraw %}
{% endhighlight %}

3.创建post_by_tag.html layout。这是用来展示包含某tag的文章。同理，可以生成post_by_category.html做类似的事。另外，分页也是这个原理

{% highlight liquid %}
{% raw %}
<h1>Articles by tag :{{ page.tag }}</h1>
<div>
    {% if site.tags[page.tag] %}
        {% for post in site.tags[page.tag] %}
            <a href="{{ post.url }}/">{{ post.title }}</a>
        {% endfor %}
    {% else %}
        <p>There are no posts for this tag.</p>
    {% endif %}
</div>
{% endraw %}
{% endhighlight %}

4.在博客里添加Category和Tag

{% highlight liquid %}
{% raw %}
---
layout: post
title: 如何使用Jekyll的Category和Tag
category: programming
tags: [jekyll, github-pages]
---
{% endraw %}
{% endhighlight %}


5.将Tag集中注册，并将属性补全。需要创建/_data/tags.yml文件，_data目录是用来存放用户自定义变量的，需要自己创建

{% highlight liquid %}
{% raw %}
- slug: github-pages
  name: GitHub Pages
  
- slug: jekyll
  name: Jekyll
{% endraw %}
{% endhighlight %}


6.为每个Tag创建一个.md模板文件，如/blog/tag/jekyll.md。当获取/blog/tag/jekyll时，会载入blog_by_tag这个模板，它会获取tag:jekyll的所有文章，category也是这个原理

{% highlight liquid %}
{% raw %}
---
layout: blog_by_tag
tag: jekyll
permalink: jekyll/
---
{% endraw %}
{% endhighlight %}

此处一定注意在_config.yml里设置relative_permalinks，否则的话，生成的category, tag模板会在根目录下

{% highlight liquid %}
{% raw %}
relative_permalinks: true
{% endraw %}
{% endhighlight %}

另外，为了让/blog/tag目录被添加到_site里，在/blog目录下要至少包含一个html，否则jekyll不会将自建的目录部署到_site里。minddust建了个index.html，这样/blog/路径就能获取所有的文章及其概要（Home和All Posts是两码事），本文沿用了其分页功能。

详细完整的实现可以查看[minddust][2]和[kthinker][3]

[1]: http://www.minddust.com/post/tags-and-categories-on-github-pages/
[2]: https://github.com/minddust/minddust.github.io
[3]: https://gitcafe.com/kthinker/kthinker/tree/gitcafe-pages


