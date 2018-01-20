---
layout: post
title: "redhat6.5从零开始离线安装qwt"
date: 2018-01-15 15:34:08 +0800
comments: true
categories: 杂
---

项目需要不联网安装 QT,Qwt ，于是就开始了折腾之旅。  

<!--more-->

大致流程如下：   
* 安装redhat_linux6.5 服务器版 
* 卸载自身的yum，使用离线rpm包重新安装CentOs的yum ，并安装downloadonly插件
* 使用yum 仅download createrepo库， libGL-devel ，gcc-c++ 
* 离线安装 createrepo 库，将下载下来的rpm 包做成本地仓库 。
* yum 安装 libGL-devel ， 离线安装gcc-c++ 
* 安装Qt 5.6 
* 安装qwt 库   


redhat 系统的安装是傻瓜式的，不用多说 。   

更换CentOs源的方法参照 http://blog.csdn.net/ggxiaobai/article/details/53507715 

需要注意的是163的网址现在已经404了，所以wget 用的 http://vault.centos.org/6.5/os/x86_64/Packages/ 

因为后面要用downloadonly 选项，所以需要下载包括4个rpm包:   
yum-3.2.29-40.el6.centos.noarch.rpm   
yum-metadata-parser-1.1.2-16.el6.x86_64.rpm  
yum-plugin-downloadonly-1.1.30-14.el6.noarch.rpm    
yum-plugin-fastestmirror-1.1.30-14.el6.noarch.rpm  

将其放到同一个文件夹中然后   
```
rpm -aq|grep yum|xargs rpm -e --nodeps #卸载自带yum
rpm -ivh *.rpm
```

完成yum的更换。  

接着在有网络的情况下 使用 yum 的 --downloadonly 选项 下载 createrepo libGL-devel ，gcc-c++ 的相关rpm 包 。   


使用离线rpm的形式安装createrepo  

参照 http://blog.csdn.net/lanjianhun/article/details/69360406 将本地的rpm 包 配置成软件仓库 。  
将 /etc/yum.repos.d/CentOS-Media.repo 中的 gpgcheck=1 修改为0 . 



然后用 yum install libGL-devel 从本地仓库中安装openGL 。  
至于g++ ，不知道为什么yum install gcc-c++ 提示找不到该库，此时参照http://blog.sina.com.cn/s/blog_c22e36090102vt0f.html  
离线手动安装gcc-c++   

两个依赖库安装完成之后，直接傻瓜安装Qt ，运行 qt-opensource-linux-x64-5.6.3.run 即可  

然后就是安装 qwt ，
```
cd qwt-6.1.3  
qmake #没有配置环境变量的话，加上绝对路径  
make
make install 
```   

验证安装:   
用qtcreator  打开 qwt下的example 目录下的任意项目 ，编译运行没有问题即可 。   



<div id="container"></div>
<link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
<script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
<script>
var gitment = new Gitment({
  id: 'location.href', // 可选。默认为 location.href
  owner: 'kevin20x2',
  repo: 'blog_comment',
  oauth: {
    client_id: '0d172133d26f520041e6',
    client_secret: '14eef65983bd9d0e16313feaffcf1684fc3713a9',
  },
})
gitment.render('container')
</script>