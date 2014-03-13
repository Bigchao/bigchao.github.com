---
layout: post
categories: datamining
title: R - Reshape2 Package
---

在之前介绍GGPLOT2的时候就提到过，数据分析和可视化之前的数据清理非常重要，Reshape2可以提供强大的帮助。

这次主要想介绍下这个Package, 作者同样是R大神Hadley Wickham，他还写过其他16个R拓展包，其中最有名的要算是ggplot2 和pylr了。
Reshape2可谓非常强大，用到的最多的Melt(),和Cast() 两个函数

Reshape2是原作者在5年之后重写的，更加提高效率。语法并没有太大改变，所以如果看到了reshape的教程，大家也可以看下，其实是通用的。

首先介绍下Melt()函数，通俗易懂的理解就是，如果不加任何参数，就是把原来的data.frame只变成三列，id，variable，value.

举个例子理解下
Smiths数据集原来是这样的
 <pre> subject time    age    weight  height
1 John Smith    1       33     90   1.87
2 Mary Smith    1   	NA     NA   1.54</pre>

我们来melt一下

{% highlight r %}	
    melt(smiths)

{% endhighlight %}
    
	
出来的结果就是这样的了

<pre>     subject variable value
1 John Smith     time  1.00
2 Mary Smith     time  1.00
3 John Smith      age 33.00
4 Mary Smith      age    NA
5 John Smith   weight 90.00
6 Mary Smith   weight    NA
7 John Smith   height  1.87
8 Mary Smith   height  1.54
</pre>

<br></br>

大家发现了什么？是不是就是先按住第一列不要动，然后第二列的列名变成了新建变量variabe的值，然后原来第二列的值变成了第三列新建变量value的值，然后后边的列都以此类推？

最浅显易懂就是这么理解了。
当然，Melt()函数可以改变的参数很多，官方给出的参数在这里

{% highlight r %}

    melt(data, <span style="color: #ff0000;">id.vars</span>,<span style="color: #ff0000;"> measure.vars</span>,
    variable.name = "variable", ..., na.rm = FALSE,
    value.name = "value")

{% endhighlight %}


注意上面，

- id.vars是说指定id的，用id可以代替id.vars，也就是例子中的subject这一列
- measure.vars是指所有原数据中，除了id.vars的所有列, 可以用measured代替measure.vars
- variable.name是指定新建variable这一列的列名的
- na.rm = TRUE 的用法是原来数据中如包含空值，新建数据集将NA的行全部删除
- value.name是指定新建变量value这一列的列名

感受下下面一段代码

{% highlight r %}

	melt1 <- melt(smiths,id=c("subject","time"),variable.name=c("newvar"),value.name=c("newval"))

{% endhighlight %}

我们重新指定了id为subject和time两个。

<pre> subject      time newvar newval
1 John Smith    1    age  33.00
2 Mary Smith    1    age     NA
3 John Smith    1 weight  90.00
4 Mary Smith    1 weight     NA
5 John Smith    1 height   1.87
6 Mary Smith    1 height   1.54</pre>

<br></br>
这样是不是非常方便把数据揉来揉去了？：）

下面介绍下cast()函数
如果说melt把数据变长，那么cast就把数据变宽。在Reshape2 Package中显而易见的一点不同是cast()函数变成了

- dcast，针对data.frame
- acast()，针对array
- 因为我们用的最多的就是data.frame,所以推荐这个。 array可以提高更多的维度。

下面给出cast的用法，用英文原文更能保留其中的原汁原味，我也懒得再绞尽脑汁想如何翻译了。

- data： the molten data set to reshape
- formula: the casting formula which describes the shape of the output format (if you omit this argument, cast will return the data frame in the classic form with measured variables in the columns, and all other id variables in the rows) 
- fun.aggregate: aggregation function to use, if necessary
- margins: what marginal values should be computed

其中的第二段太难理解了，说白了就是你想怎么molten data重新整成什么样。 所谓的fomula其实就是这样的：col_var_1 + col_var_2 ~ row_var_1 + row_var_2

- 大家现在可以停下来想一想，cast的过程就是把原来的melt的逆过程，
- 其中“~” 这个符号就是分隔符
- 也就是说在 ~ 之前是留着不懂的，~之后指示为在原来“row_var_1”这一列所有的值都变成了单独列。
- 有时候于遇到 "..."符号，表示fomula中所有没有用到的vector都在此。"."表示在后面没有任何的数据列

{% highlight r %}

	cast(ffm, treatment + rep ~ ., length)
	#表示在分别统计treatment，和rep两个变量的频度
	#主要感受下 "." 的用法 
	相当于SQL语句里面的
	select treatment，rep count（*） as length
	from ffm	
	group by treatment, rep;

{% endhighlight %}


举个例子！
刚才已经建立了一个melt1的数据集，现在我们想把它还原

{% highlight r %}	
	> dcast(melt1, time + subject ~ newvar)	
	
{% endhighlight %}

这样就可以还原到了，在逗号后面的就是所谓的fomula。
看第二个例子

{% highlight r %}

	> dcast(melt1, ... ~ subject)
	# 感受下"..." 的用法

{% endhighlight %}
这回用到了...的用法，另外还有把数据框
	
<pre>  time newvar John Smith Mary Smith
1    1    age      33.00         NA
2    1 weight      90.00         NA
3    1 height       1.87       1.54 </pre>

<br></br>
关于第一二点，不知道这样大家是否可以理解了呢？


其中第三点和第四点稍微举了例子大家就能理解了

{% highlight r %}
	
	#用到airquality数据集
	names(airquality) <- tolower(names(airquality))
	因为列名中包含大小写，所以为了书写方便全部用tolower()函数改成小写
	aqm <- melt(airquality, id=c("month", "day"), na.rm=TRUE)
	dcast(aqm, month ~ variable, mean, 
	+ margins = c("month", "variable"))

{% endhighlight %}

其实就是根据每月的各个variable求了个平均值，特别像数据透视表

<pre> 
  month    ozone  solar.r      wind     temp    (all)
1     5 23.61538 181.2963 11.622581 65.54839 68.70696
2     6 29.44444 190.1667 10.266667 79.10000 87.38384
3     7 59.11538 216.4839  8.941935 83.90323 93.49748
4     8 59.96154 171.8571  8.793548 83.96774 79.71207
5     9 31.44828 167.4333 10.180000 76.90000 71.82689
6 (all) 42.12931 185.9315  9.957516 77.88235 80.05722</pre>


<br></br>

当然这个拓展包中还有其他若干好用的函数，有兴趣的话可以看看下面两个文档

- 官方的文档链接在: [这里](cran.r-project.org/web/packages/reshape2/reshape2.pdf‎)

- 作者同样写过一篇发表在Journal of Statistical Software的论文，比官方文档写的更加清楚一点。链接在: [这里](http://www.jstatsoft.org/v21/i12/paper) 强烈推荐这篇论文，看完之后就会觉得通透了！

