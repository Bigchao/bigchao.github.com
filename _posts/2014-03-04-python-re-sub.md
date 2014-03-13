---
layout: post
categories: datamining
title: python正则表达式：re.sub
---

re.sub很强大，所以导致用法稍微有点复杂。所以当遇到稍微复杂的用法时候，就容易犯错。

所以此处，总结一下，在使用re.sub的时候，需要注意的一些事情。

re.sub共有五个参数。

其中三个必选参数：pattern, repl, string

两个可选参数：count, flags

re.sub(pattern, repl, string, count=0, flags=0)
Return the string obtained by replacing the leftmost non-overlapping occurrences of pattern in string by the replacement repl. If the pattern isn’t found, string is returned unchanged. repl can be a string or a function; if it is a string, any backslash escapes in it are processed. That is, \n is converted to a single newline character, \r is converted to a carriage return, and so forth. Unknown escapes such as \j are left alone. Backreferences, such as \6, are replaced with the substring matched by group 6 in the pattern. For example:

{% highlight python %}
    >>> re.sub(r'def\s+([a-zA-Z_][a-zA-Z_0-9]*)\s*\(\s*\):',
    ...        r'static PyObject*\npy_\1(void)\n{',
    ...        'def myfunc():')
'static PyObject*\npy_myfunc(void)\n{'
{% endhighlight %}


If repl is a function, it is called for every non-overlapping occurrence of pattern. The function takes a single match object argument, and returns the replacement string. For example:

{% highlight python %}
>>> def dashrepl(matchobj):
...     if matchobj.group(0) == '-': return ' '
...     else: return '-'
>>> re.sub('-{1,2}', dashrepl, 'pro----gram-files')
'pro--gram files'
>>> re.sub(r'\sAND\s', ' & ', 'Baked Beans And Spam', flags=re.IGNORECASE)
'Baked Beans & Spam'
{% endhighlight %}

The pattern may be a string or an RE object.
The optional argument count is the maximum number of pattern occurrences to be replaced; count must be a non-negative integer. If omitted or zero, all occurrences will be replaced. Empty matches for the pattern are replaced only when not adjacent to a previous match, so sub('x*', '-', 'abc') returns '-a-b-c-'.
In addition to character escapes and backreferences as described above, \g<name> will use the substring matched by the group named name, as defined by the (?P<name>...) syntax. \g<number> uses the corresponding group number; \g<2> is therefore equivalent to \2, but isn’t ambiguous in a replacement such as \g<2>0. \20 would be interpreted as a reference to group 20, not a reference to group 2 followed by the literal character '0'. The backreference \g<0> substitutes in the entire substring matched by the RE.

下面详细介绍一下这5个参数的基本配置

**第一个参数：pattern**

pattern，表示正则中的模式字符串，这个没太多要解释的。

需要知道的是：

反斜杠加数字（\N），则对应着匹配的组（matched group）

- 比如\6，表示匹配前面pattern中的第6个group
- 意味着，pattern中，前面肯定是存在对应的，第6个group，然后你后面也才能去引用

比如，想要处理：

>hello 
>, nihao crifan

且此处的，前后的crifan，肯定是一样的。

而想要把整个这样的字符串，换成crifanli

则就可以这样的re.sub实现替换：

{% highlight python %}
inputStr = "hello crifan, nihao crifan";
replacedStr = re.sub(r"hello (\w+), nihao \1", "crifanli", inputStr);
print "replacedStr=",replacedStr; #crifanli
{% endhighlight %}

**第二个参数：repl**

repl，就是replacement，被替换，的字符串的意思。

repl可以是字符串，也可以是函数。

如果repl是字符串的话，其中的任何反斜杠转义字符，都会被处理的。

即：

- \n：会被处理为对应的换行符；
- \r：会被处理为回车符；
其他不能识别的转移字符，则只是被识别为普通的字符：
比如\j，会被处理为j这个字母本身；
- 反斜杠加g以及中括号内一个名字，即：\g<name>，对应着命了名的组，named group
接着上面的举例：

想要把对应的：

>hello crifan, nihao crifan

中的crifan提取出来，只剩：

>crifan

就可以写成：

{% highlight python %}
inputStr = "hello crifan, nihao crifan";
replacedStr = re.sub(r"hello (\w+), nihao \1", "\g<1>", inputStr);
print "replacedStr=",replacedStr; #crifan
{% endhighlight %}

对应的带命名的组（named group）的版本是：
{% highlight python %}
inputStr = "hello crifan, nihao crifan";
replacedStr = re.sub(r"hello (?P<name>\w+), nihao (?P=name)", "\g<name>", inputStr);
print "replacedStr=",replacedStr; #crifan
{% endhighlight %}

当然repl也可以是函数

举例说明：

比如输入内容是：

>hello 123 world 456

想要把其中的数字部分，都加上111，变成：

>hello 234 world 567

那么就可以写成：
{% highlight python %}
#!/usr/bin/python
# -*- coding: utf-8 -*-
"""
Function:
【整理】详解Python中re.sub
 
"""
 
import re;
 
def pythonReSubDemo():
    """
        demo Pyton re.sub
    """
    inputStr = "hello 123 world 456";
     
    def _add111(matched):
        intStr = matched.group("number"); #123
        intValue = int(intStr);
        addedValue = intValue + 111; #234
        addedValueStr = str(addedValue);
        return addedValueStr;
         
    replacedStr = re.sub("(?P<number>\d+)", _add111, inputStr);
    print "replacedStr=",replacedStr; #hello 234 world 567
 
###############################################################################
if __name__=="__main__":
    pythonReSubDemo();
{% endhighlight %}

**第三个参数：string**

string，即表示要被处理，要被替换的那个string字符串。

没什么特殊要说明。

**第四个参数：count**

举例说明：

继续之前的例子，假如对于匹配到的内容，只处理其中一部分。

比如对于：

>hello 123 world 456 nihao 789

只是像要处理前面两个数字：123,456，分别给他们加111，而不处理789，

那么就可以写成：
{% highlight python %}
#!/usr/bin/python
# -*- coding: utf-8 -*-
import re;
 
def pythonReSubDemo():
    """
        demo Pyton re.sub
    """
    inputStr = "hello 123 world 456 nihao 789";
     
    def _add111(matched):
        intStr = matched.group("number"); #123
        intValue = int(intStr);
        addedValue = intValue + 111; #234
        addedValueStr = str(addedValue);
        return addedValueStr;
         
    replacedStr = re.sub("(?P<number>\d+)", _add111, inputStr, 2);
    print "replacedStr=",replacedStr; #hello 234 world 567 nihao 789
 
###############################################################################
if __name__=="__main__":
    pythonReSubDemo();
{% endhighlight %}

**第五个参数：flags**
此处没有什么可以特别讲的

要注意，被替换的字符串，即参数repl，是普通的字符串，不是pattern


{% highlight python %}
re.sub(pattern, repl, string, count=0, flags=0)
{% endhighlight %}
即，对应的第二个参数是repl。

需要你指定对应的r前缀，才是pattern：

r"xxxx"

不要误把第四个参数flag的值，传递到第三个参数count中了
遇到的问题：

当传递第三个参数，原以为是flag的值是，

结果实际上是count的值

所以导致re.sub不功能，

所以要参数指定清楚了：

{% highlight python %}
replacedStr = re.sub(replacePattern, orignialStr, replacedPartStr, flags=re.I); # can omit count parameter
{% endhighlight %}

或者：

{% highlight python %}
replacedStr = re.sub(replacePattern, orignialStr, replacedPartStr, 1, re.I); # must designate count parameter
{% endhighlight %}

才可以正常工作。

延伸阅读：
---
[详解Python中的re.sub](http://www.crifan.com/python_re_sub_detailed_introduction/)
