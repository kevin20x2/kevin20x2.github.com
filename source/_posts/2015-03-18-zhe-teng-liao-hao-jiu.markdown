---
layout: post
title: "折腾了好久"
date: 2015-03-17 15:26:14 +0800
comments: true
categories: 杂
---

翔在github上搞了一个blog ，然后我也就闲的蛋疼也想搞一个了。

然后真的就是蛋疼了。
--
写这篇blog简直也是蛋疼的一部分。
<!--more-->

**首先我不会markdown**

还好有markdownpad 要不然每一次都要rake preview一次简直蛋疼。

嘛，将就着写吧。

---

作为一个抄袭作品简直失败

因为抄袭的对象用的是mac【默默鄙视一下

然后给的教程不能用，然后只能在网上找[教程](http://shanewfx.github.io/blog/2012/02/16/bulid-blog-by-octopress/)了

还好之前有用git的经历。

然后就是各种问题了，编码问题我用的是*Jekyll 2.5.3* ， 把config.yml和post的markdown文件改成utf-8编码之后算是好歹能用中文了。

####西卡西  
因为要把终端编码改成 utf-8 导致终端不能输中文了。（不能调出输入法）
####但是可以从其他地方粘贴啊
我觉得问题好像就这样解决了，但是我错了，比如这篇blog名字叫 "折腾了好久" 本来名字是叫 "折腾了一下午"的

为毛要换名字 ？
####因为 rake new_post["折腾了一下午"] 会报错 但是rake new_post["折腾了好久"] 就没问题

###WTF！！

在次，github上的仓库名称必须得是username.github.com天真的我还以为可以随便起名字，然后差不多就在原地打转了，算起来一共折腾了大半天。

然后我这个blog暂时还只是兴趣产物，目测过不了多久就会断更(汗)

然后内容大概会放一下题解吧。

最后嘛，反正blog的各种东西还不太清楚，貌似现在还没评论功能。慢慢搞咯 _(:з」∠)_

##更新
第一篇文章上传成功 ，第二篇题解就跪了(汗)  
rake generate 的时候warning 没解决  
各种百度发现是加了代码的锅。  
Jekyll 默认的代码高亮的插件是 pygments 一个python插件
这个不知道为啥不能正常工作 ，然后我就换成了另一个 rouge  
详细教程在 [__这里__](http://jekyll-windows.juthilo.com/3-syntax-highlighting/)  
看来还有各种问题要解决Orz

###3月22号更新
今天尝试加上一篇blog zoj3369   
reke generate 
妈个鸡居然报错了 。
####jekyll 2.5.3 | Error:  undefined method `[]' for nil:NilClass  
百度谷歌各种 发现说这是个高亮插件的错误，但是我已经把插件换成了rouge而且成功上传了两篇文章 。这么说只能是这篇新博客的问题。
然后我把它剪切了出去  
rake generate  
__成功了__  
我真是 (╯‵□′)╯︵┻━┻︵┻━┻

然后把新文件稍微改了一下
把其中的 50\*50\*50\*50
改成了  50\\\*50\\\*50\\\*50
似乎就成功了。已经无力吐槽了，下次注意。

###3月23日更新
尝试加上了侧边的分类栏  
之后在每篇文章categoris: 上面加上分类标签就好了
具体见 [这里](http://codemacro.com/2012/07/18/add-category-list-to-octopress/)  



