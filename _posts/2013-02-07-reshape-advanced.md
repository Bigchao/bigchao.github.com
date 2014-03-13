---
layout: post
categories: datamining
title: R - Reshape Package II
---

上次提到了Reshape包中的强大的cast函数。由于时间关系，对fun.aggreate，drop，subset等函数没有完全讲清。今天补上

先看下面的一个例子体会一下

{% highlight r %}

    library(reshape)
	# example table with non-unique row-names
	tab <- data.frame(gene=rep(letters[1:3], each=3), s1=runif(9), s2=runif(9))
	# melt
	tab.melt <- melt(tab, id=1)
	# function to summarize with logic: 
	# mean if max/min < 1.5, else median
    # 在max/min 小于1.5的时候取均值，而其他情况去中位数
	summarize <- function(x){ifelse(max(x)/min(x)<1.5, mean(x), median(x))}
	# ifelse函数的通用用法： ifelse（test, yes,no)
	# cast with summarized values
	dcast(tab.melt, gene~variable, summarize)

{% endhighlight %}



这里强大之处在于，在最后dcast的时候，我们自定义了一个统计函数，而没有用到原来比较简单的mean,max等等。

但是注意这样一个情况，在运行的时候会出现这样的报错

{% highlight html %}

	Error in vapply(indices, fun, .default) : 
  	values must be type 'logical',
	 but FUN(X[[1]]) result is type 'double'
	In addition: Warning messages:
	1: In max(x) : no non-missing arguments to max; returning -Inf
	2: In min(x) : no non-missing arguments to min; returning Inf

{% endhighlight %}


这里讲到了`fill`的情况，原帖在[这里](http://stackoverflow.com/questions/4835202/error-with-custom-aggregate-function-for-a-cast-call-in-r-reshape2/4835804#4835804?s=7400121f-cdc1-4f76-8bf3-1fc020e44d48)

注意把代码改为

{% highlight r %}

	dcast(tab.melt, gene~variable, summarize, fill=NaN)

{% endhighlight %}

其实说的道理就是要提供给fill一个参数，原来default的参数是不行的，不一定fill=NaN，我试了下fill=o，fill=1，等等任意一个情况都是可行的。

这样可以体会到Reshape 自定义的强大了吧！

---

在Reshape2拓展包中，还有几个非常有用的函数

**colsplit**：把变量分开到不同的列

{% highlight r %}
	
	x <- c("a_1", "a_2", "b_2", "c_3")
	vars <- colsplit(x, "_", c("trt", "time"))

{% endhighlight %}

运行之后就出现了如下结果，是不是很炫？！
<pre>
 	trt	time
1	a	1
2	a	2
3	b	2
4	c	3
</pre>

<br></br>
**Recast**： 在一步中实现melt和cast功能

{% highlight r %}

	recast(data, formula, ..., id.var, measure.var)

{% endhighlight %}


例子

{% highlight r %}

	recast(french_fries, time ~ variable, id.var = 1:4)

{% endhighlight %}

