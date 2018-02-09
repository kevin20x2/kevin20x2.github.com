---
layout: post
title: "Windows10 搭建Unity android 环境"
date: 2018-02-09 22:16:08 +0800
comments: true
categories:  杂
---

Unity 环境为2017.2.0f3   
VS版本是2017 社区版 
<!--more-->

本人是直接按照unity 的引导一步步来的，步骤如下 :    
## 安装jdk  
---

直接去[官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)安装最新的JDK 即可。   

## 安装Android 相关工具   
---   
由于家里电信的网络对于相关网站的网路速度有影响，这里是通过sdk-tools 来安装的。  
1. 去 http://www.android-studio.org/ 安装sdk-tools.
2. 将zip压缩包解压至某一目录(如:C:\\sdk-tools),这时的目录结构应该是 C:\\sdk-tools\\tools
3. 打开命令行 ,切换目录到tools\bin 下
运行 
```bash
sdkmanager --list 
``` 
会发现显示了当前的安装状况，现在需要安装的有:   
安卓的平台：platforms;android-27   
构建工具:  build-tools;27.0.3  
这里我直接选了当前最新的版本。  
安装方法是 
```bash
sdkmanager "platforms;android-27"
sdkmanager "build-tools;27.0.3"   
#如果不安装build-tools 会报没有aapt.exe的错误
```   


## 在unity 编辑器中指定相关路径   
在构建的时候会提示你选择 sdk 和jdk 路径，sdk 的话制定的是上文的   
C:\\sdk-tools (自行修改目录)   
jdk的话制定的是   
C:\Program Files\Java\jdk1.8.0_161 (自行修改安装目录)  

这中间的坑有:  

---
1. 没有安装build-tools   
```
Win32Exception: ApplicationName='C:/sdk-tools\platform-tools\aapt.exe', CommandLine='package --auto-add-overlay -v -f -m -J "gen" -M "AndroidManifest.xml" -S "res" -I "C:/sdk-tools\platforms\android-27\android.jar" -F bin/resources.ap_', CurrentDirectory='Temp/StagingArea
```  
会导致上述错误  

2. 检测到的sdk 的tools的版本不对   
这里我的解决方法是用现在的sdk tools 安装最新的sdk tools: 
```bash
sdkmanager "tools"
```
**注意**:由于新安装tools 会删除原来的文件，然而现在正在运行的tools就占用了tools文件夹，因此需要先将tools文件夹重新命名(如: tools-old) 然后再在tools-old\\bin中执行上面的命令。  

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
  





