---
layout: post
categories: datamining
title: Plyr- 数据整合利器
---

Hadley Wickham写的Plyr拓展包，是所有拓展包里下载最多的。联合上Reshape2，和ggplot2，devtools已经成为业界口碑最好的。

在开始介绍plyr拓展包之前，先简单介绍一种数据预处理和数据清洁的思想

----
###S-A-C

S： Split 即为把数据切片等待处理

A： Apply 对于每一个片层进行数据的运算

C： Combine 把结果进行相加

我们先看一个引例:

用到的是R自带的Iris数据集

{% highlight r %}
	
	head(iris)

{% endhighlight %}

<pre>
  Sepal.Length Sepal.Width Petal.Length Petal.Width Species
1          5.1         3.5          1.4         0.2  setosa
2          4.9         3.0          1.4         0.2  setosa
3          4.7         3.2          1.3         0.2  setosa
4          4.6         3.1          1.5         0.2  setosa
5          5.0         3.6          1.4         0.2  setosa
6          5.4         3.9          1.7         0.4  setosa
</pre>
<br>
<br>

原数据有150行，有5个字段，其中Species中有setosa,virginica,versicolor三种。

**任务**： 计算在Species字段中，计算每一种物种类别Sepal.Length的平均值

S：把原数据切片，按照Species的三类分为三个子数据集

A：在每一个数据集中计算Sepal.Length的平均值

C：把三个计算后的结果再放回到一起

也就是说我们要的结果是下面这个样子的。

<pre>
     Species meanSL     
1     setosa  5.006
2 versicolor  5.936 
3  virginica  6.588 
</pre>
<br>

**这个思想很重要，切片，计算，整合。**

不管是用到什么方法来具体实现，基本都是遵循这个思想。

我们先来看一种最简单粗暴的实现方法。

{% highlight r %}
	
	# Split the data according to Species
	splitedIris <- split(iris$Sepal.Length, iris$Species)
	# Apply the mean function to each  individual pieces
	m1 <- mean(splitedIris[[1]])
	m2 <- mean(splitedIris[[2]])
	m3 <- mean(splitedIris[[3]])
	# 注意上面，是用两个方括号，单个方括号返回列表，而两个方括号返回列表的提取对象
	# Combine the results
	c(m1, m2, m3)
    
{% endhighlight %}

上面就是最直观的运用这种思想来实现的方法。

当然我们可以进行小小的改进，用一个循环来稍微简化下，看如下代码

{% highlight r %}
	
	splitedIris <- split(iris$Sepal.Length, iris$Species)
	res <- c()
	for (i in 1:length(splitedIris)) {
	res[i] <- mean(splitedIris[[i]])
	}
   
{% endhighlight %}


在我们开始学习R的时候，就有一句**懒人法则**

**尽量不要用循环！**

因为基本上可以循环的已经被写成拓展包可以直接调用了。

我们可以用到的高阶函数如 `by`,`aggregate`,`tapply`等等吧


{% highlight r %}
	
	# 用by函数来实现
	by(iris$Sepal.Length, iris$Species, mean)
	# 用tapply函数实现
 	tapply(iris$Sepal.Length, iris$Species, mean)
	
{% endhighlight %}

当然实现的方法很多种，还有不少package可以实现

但是我们的主角终于要登场了！

###[Plyr](http://plyr.had.co.nz/)

此包一出，其他都可以洗洗睡了。

这个包里有若干函数，但是我们主要讲的就是`ddply`函数

有关其他函数命名原则可以看[肖凯老师的博客](http://xccds1977.blogspot.hk/2012/04/plyrapply.html)

**ddply(.data, .variables, .fun = NULL,…)**

- .data ： 原来的数据集
- .variables : 如何对原数据进行切片
- .fun : 对切片的数据如何进行再操作


{% highlight r %}
	
	ddply(iris, .(Species), summarize, meanSL = mean(Sepal.Length), sdSL = sd(Sepal.Length))
	
{% endhighlight %}

这里边我们可以分解看下，

- iris是原数据集
- .(Species) 是对Species这个字段进行切片
- summarize 是一个函数关键字，表示新建一个新的data.frame。 在plyr里与之相对的是`transform`，表示在原有数据集中进行修改
- meanSL，sdSL就是如何计算产生新的字段并最后整合


不仅可以这样，还可以在`Apply`这一部自定义函数

{% highlight r %}
	
	ddply(iris, .(Species), transform, SLZ = (Sepal.Length - mean(Sepal.Length)) / sd(Sepal.Length))
	
{% endhighlight %}


上面我就自己造了一个函数 来计算Z-Score。

最后的结果是这个样子的。


<pre>
      Sepal.Length Sepal.Width Petal.Length Petal.Width    Species         SLZ
1            5.1         3.5          1.4         0.2     setosa     0.26667447
2            4.9         3.0          1.4         0.2     setosa 	-0.30071802
3            4.7         3.2          1.3         0.2     setosa 	-0.86811050
4            4.6         3.1          1.5         0.2     setosa 	-1.15180675
5            5.0         3.6          1.4         0.2     setosa 	-0.01702177
6            5.4         3.9          1.7         0.4     setosa 	 1.11776320
7            4.6         3.4          1.4         0.3     setosa 	-1.15180675
8            5.0         3.4          1.5         0.2     setosa 	-0.01702177
9            4.4         2.9          1.4         0.2     setosa 	-1.71919923
10           4.9         3.1          1.5         0.1     setosa 	-0.30071802

</pre>

<br>
这样一来也可以直观的看出来`Summarize`和`Transform`差别了。


大家先体会一下plyr包的强大之处，我们在下一篇博文里继续介绍一些高阶用法

- 强烈建议大家读一下原作者的这篇[Paper](www.jstatsoft.org/v40/i01/paper‎
)
- 还有这个[Tutorial](http://streaming.stat.iastate.edu/workshops/r-intro/lectures/6-advancedmanipulation.pdf)



 








