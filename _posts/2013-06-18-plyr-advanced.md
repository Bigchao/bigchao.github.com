---
layout: post
categories: datamining
title: Plyr- 多因子整合
---

在上次我们看到的例子，Iris数据集中只有Species一个是因子 （factor），其实plyr更加适合于在多因子的数据集中进行操作

今天我们用一个稍微复杂一点的数据集来详细讲解

- [babynames](https://github.com/hadley/data-baby-names/blob/master/baby-names.csv)

原数据大约2万5千行，包括四个字段year，name，pecent，sex。其中year是从1880到2008


{% highlight r %}
	
	options(stringsAsFactors = FALSE)
	babyname <- read.csv("bnames.csv")
	head(babyname)

{% endhighlight %}

数据就是这个样子的

<pre>
  year    name  	percent 	sex
1 1880    John 		0.081541 	boy
2 1880 William 		0.080511 	boy
3 1880   James 		0.050057 	boy
4 1880 Charles 		0.045167 	boy
5 1880  George 		0.043292 	boy
6 1880   Frank 		0.027380 	boy
</pre>
<br></br>

**问题1**： 找到每一年，每种性别中最受欢迎的名字

还是按照我们的SAC思想

S：把原数据按照sex和year两个因子进行切片

A：每个切片中得到最受欢迎的名字

C：把结果整合


{% highlight r %}
	
	ddply(babyname, .(year, sex), ?????)

{% endhighlight %}

前两部份我们都已经很熟悉了，原数据切片。但是后面得到最受欢迎的名字应该怎么办？

我们要自定义一个函数，来求出最受欢迎的人名，然后再把它加到ddply中的function里面

{% highlight r %}
	
	mostPopular <- function(x) {
	x$name[which.max(x$percent)]
	}
	ddply(babyname, .(year, sex), mostPopular)

{% endhighlight %}

结果一下就出来了（截取）
<pre>
   year  sex       V1
1   1880  boy     John
2   1880 girl     Mary
3   1881  boy     John
4   1881 girl     Mary
5   1882  boy     John
6   1882 girl     Mary
7   1883  boy     John
8   1883 girl     Mary
9   1884  boy     John
10  1884 girl     Mary
11  1885  boy     John
12  1885 girl     Mary
13  1886  boy     John
14  1886 girl     Mary
15  1887  boy     John
16  1887 girl     Mary
17  1888  boy     John
18  1888 girl     Mary
19  1889  boy     John
20  1889 girl     Mary
</pre>
<br></br>

可以看出，1889年，都是男的叫John最多，女的叫Mary最多。


**问题2**：每一年，每种性别中 （ 作者注： 这就是告诉你怎么切片呢 ）， 名字的首字母所占比例是多少？ 

解释下问题： Get the total percentage of the first character of the baby names by year and gender

就比如2000年一共15个男孩出生，5个叫杨超，5个叫贾超，5个叫牛超

那Y（Yang）的首字母占比是 1/3
同理 J （Jia ） 和 N （Niu ）也是 1/3


还是SAC思想

S： 按照性别、年份选出进行切片。
**注意！** 这里不仅仅是要对这两个变量进行切片，还有自定义一个新的变量 `姓名的首字母`！

请在反思上面的简单引例。

A： 对同样的首字母百分比进行加和

C： 数据整合



{% highlight r %}
	
	#首先提取名字的首字母
	firstletter <- function(x) {
	tolower(substr(x,1,1))
	}
	# 根据ddply可以得出结果
	letterFreq <- ddply(babyname, .(year, sex, first=firstletter(name)), summarize, sumPer = sum(percent))


{% endhighlight %}


最后的结果就是这样的

<pre>
  year sex 	first   sumPer
1 1880 boy     a 	0.062543
2 1880 boy     b 	0.017858
3 1880 boy     c 	0.084047
4 1880 boy     d 	0.021010
5 1880 boy     e 	0.058219
6 1880 boy     f 	0.055142
</pre>
<br></br>
最后我们可以可视化一下，用到ggplot2

{% highlight r %}
	
	require(ggplot2)
	ggplot(letterFreq, aes(x=year, y=sumPer, group=sex)) + geom_line(aes(color=sex)) + facet_wrap(~ first, nrow=3)

{% endhighlight %}

![](/assets/images/plyr-advanced/eQloL6A.png)


在Hadley的官网上，有不少这样的例子，大家一定要多去看看，多感受就更有感觉了。

原文的数据，代码，最后的图在[这里](http://pan.baidu.com/s/19r800)

