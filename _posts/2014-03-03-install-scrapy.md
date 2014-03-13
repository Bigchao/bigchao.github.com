---
layout: post
categories: datamining
title: scrapy初探-安装和简单操作
---

最近因为要抓取一些网页，所以搜索一些工具来实现。当然最熟悉的R也可以胜任这个事儿，比如Rcurl和XML的包都行。但是其功能还是挺受局限的，尤其和Python相比，就差的比较多了。

Python的beautiful soup库其实也很不错。但是Scrapy似乎很炫的样子，不少人和我推荐了。所以就趁着项目之前的筹备期先学起来。

首先是安装。

-- Windows

我的电脑是windows8.1, 安装的时候我参考了多个网上的版本。用了将近两天时间，最后宣告失败。就是装不上。

要说现在留在windows的最重要理由就是SAS了。

-- Mac

Mac上安装其实则方便很多。

但是事情没有我想象的那么简单，在Mac上安装也耗费了我不少的时间。一度情绪相当低落相当崩溃。

因为Mac自带了Python2.7.5所以当时我就直接用这个版本的python了。然后安装了pip，然后按照


{% highlight python %}
	pip install scrapy
{% endhighlight %}

就可以了。就不是不行。

后来出现了其他诸多问题。

最后的办法就是所有都推到重新来，

1. 安装了Homebrew，开始homebrew都安装不了，一度非常崩溃，更新了Xcode最新版之后可以了
2. brew python

然后一点点就可以了。

安装过程整整花了我两天时间，当最后可以使用的时候已经身心俱疲了。

下面就是使用过程。

整个的scrapy的构架和Django有点像，有人更是说其中的机制是模仿了Django。先建立一个工程然后把要提取的放到不同的文件里。代码量并不大。而且很方便。

下面简单走一个流程，看看scrapy是怎么运作的？

### 找一个要抓取的网站

按照官方放的教程，我就以`mininova`为例

[http://www.mininova.org/today]()

### 定义我们要抓取的内容

我们想文件的url，名称，描述，和大小。所以在项目文件夹下的`item.py`中添加以下内容

{% highlight python %}
from scrapy.item import Item, Field

class TorrentItem(Item):
    url = Field()
    name = Field()
    description = Field()
    size = Field()
{% endhighlight %}


### 定义爬虫
定制爬虫就是定义如何搜索链接和如何选取其中的数据。其中需要用到Xpath和正则的知识。

首先分析页面内容：

[http://www.mininova.org/tor/2676093]()


比如这个网站

Chrom可以直接观源代码

#####文件名称在tag h1 中
{% highlight html %}
<h1>Darwin - The Evolution Of An Exhibition</h1>
{% endhighlight %}

提取应该这样：
`//h1/text()`

##### 描述在 <div> tag with id="description":

{% highlight html %}
<h2>Description:</h2>

<div id="description">
Short documentary made for Plymouth City Museum and Art Gallery regarding the setup of an exhibit about Charles Darwin in conjunction with the 200th anniversary of his birth.

...
{% endhighlight %}

所以Xpath就应该是这样：

`//div[@id='description']`

##### 最后，有关文件大小
{% highlight html %}
<div id="specifications">

<p>
<strong>Category:</strong>
<a href="/cat/4">Movies</a> &gt; <a href="/sub/35">Documentary</a>
</p>

<p>
<strong>Total size:</strong>
150.62&nbsp;megabyte</p>
{% endhighlight %}

Xpath提取就应该是这样的：

`//div[@id='specifications']/p[2]/text()[2]`

最后爬虫文件应该长这个样子：

{% highlight python %}
from scrapy.contrib.spiders import CrawlSpider, Rule
from scrapy.contrib.linkextractors.sgml import SgmlLinkExtractor
from scrapy.selector import Selector

class MininovaSpider(CrawlSpider):

    name = 'mininova'
    allowed_domains = ['mininova.org']
    start_urls = ['http://www.mininova.org/today']
    rules = [Rule(SgmlLinkExtractor(allow=['/tor/\d+']), 'parse_torrent')]

    def parse_torrent(self, response):
        sel = Selector(response)
        torrent = TorrentItem()
        torrent['url'] = response.url
        torrent['name'] = sel.xpath("//h1/text()").extract()
        torrent['description'] = sel.xpath("//div[@id='description']").extract()
        torrent['size'] = sel.xpath("//div[@id='info-left']/p[2]/text()[2]").extract()
        return torrent

{% endhighlight %}

{% highlight python %}
scrapy crawl mininova -o scraped_data.json -t json
{% endhighlight %}

最后保存的数据为jason格式。

{% highlight html %}
[{"url": "http://www.mininova.org/tor/2676093", "name": ["Darwin - The Evolution Of An Exhibition"], "description": ["Short documentary made for Plymouth ..."], "size": ["150.62 megabyte"]},
# ... other items ...
]
{% endhighlight %}

参考
---
[scrapy](http://scrapy.org/)