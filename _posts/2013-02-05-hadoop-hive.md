---
layout: post
categories: related
title: 大数据体验之Hadoop, Rhadoop, 和Hive
---

####Hadoop配置
最近开始学习Hadoop。走了不少弯路，也有很多收获。现在把我从linux完全小白到安装成功的过程和大家分享一下。

[1](http://weixiaolu.iteye.com/blog/1401931)

[2](http://weixiaolu.iteye.com/blog/1401786)

---
这篇文章还是讲的很好的。但是唯一有一点遗憾的是我没有实现在hadoop用户组完成无密码登录。也就是ssh那一步完成的有些问题，不过目前看来不是很影响。
在安装Hadoop之前有一点很重要，就是一定要安装好JDK，也就是Java的运行环境。
我在这部浪费了很长时间，因为网上有不同的安装方法，每次到运行的时候却总说没有安装。
最后我还是采取了最笨的安装方法


所有的安装路径都在 root权限下
	`/usr/local`

因为保险起见，实在失败次数太多而无法找到原因，我连下载的JDK都和原来的作者是一模一样的。没有下载后缀名是.tar.gz 或者 .rfm的文件。

安装好JDK之后就按部就班的把Hadoop一步一步安装好。
注意，在我给的链接当中并没有把hadoop设置到环境变量中去，这点要稍微注意一下。
hadoop的启动路径 `/usr/local/hadoop/bin/all-star.sh`
启动并输入 jps 如果可以出现若干进程，就说明Hadoop已经配置成功了。


Rhadoop 的设置基本安装

[3](http://cos.name/2013/03/rhadoop2-rhadoop/)

一步一步来的。

有个小小的问题要注意。因为要用Root权限，所以有一些

作者的另一篇问题给出了相应的解答

[4](http://blog.fens.me/category/r-lang/)

就是要在root用户下来进行

`根据作者的回答我们发现，错误是由于：“R的类库在特定的用户下面，而不是标准合位置，导致类库不能被hadoop找到”。

在我们的环境中，发现也是同样的配置，与错误描述一致。`

这里稍微注意一下。
切换root用户，安装即可。

我在装完之后还缺少一个依赖库bitops，文中没有提到
在r中安装即可


###Hive配置
---
[1](http://blog.csdn.net/gudaoqianfu/article/details/7319287)

有了Hadoop的基础，其他就相对简单。
但是hive装好启动之后还是会报错。
WARNING: org.apache.hadoop.metrics.jvm.EventCounter is deprecated. Please use org.apache.hadoop.log.metrics.EventCounter in all the log4j.properties files.
虽然不是什么大问题，但是也觉得很不爽。
按照网上给出的方案我都试过了，但是还是不行。最后放弃解决，不影响使用就行了。

在hive指令里打出 show table;
注意一定要有分号。然后可以显示OK表明Hive安装成功了。

但是有个小问题就是
用Mysql来代替dery数据源的设置还没有做，留着以后再搞。







