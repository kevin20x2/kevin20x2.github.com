---
layout: post
title: "解决markdown 和latex 冲突的问题"
date: 2018-01-20 20:14:36 +0800
comments: true
categories: 杂
---

最近配置了 VSCODE 用来写blog ，毕竟有实时预览和数学公式的插件，但是在写论文翻译的时候，在VSCODE 下预览没问题的地方部署的时候确没有正确转义，于是想着怎么解决这个问题。   

<!--more-->
---
最开始以为是 MathJax 的问题，后来换了Katex ，照着github 上的教程加了一些js 代码进去，发现有的能够正常显示，有的还是不行，效果跟Katex 一致。 后来直接百度发现 是 Markdown 转换 成 HTML 和latex 公式引擎转换的问题。    

看了一下生成的html 文件，发现公式中的 ‘_’ 下划线有些被替代成了 \<em\>标签。这里就是问题所在了。      
参照[这里](https://liam0205.me/2015/09/09/fix-conflict-between-mathjax-and-markdown/)   
其思路是先用 markdown 中的 ` 反括号取消转义 ，表现在html上是用\<code\> 标签页包含要取消转义的部分，  
用 js 做正则匹配，获取html 中 \<code\>标签页中的\$\$ 字符，然后调用 MathJax 的js 代码来渲染公式。  

因为我用的是katex ，所以加上的代码稍有不同，也没有做正则匹配， 直接将原来有公式的地方 用 ` 反转义就好了。   


```js
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.9.0-beta/katex.min.css" integrity="sha384-L/SNYu0HM7XECWBeshTGLluQO9uVI1tvkCtunuoUbCHHoTH76cDyXty69Bb9I0qZ" crossorigin="anonymous">
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.9.0-beta/katex.min.js" integrity="sha384-ad+n9lzhJjYgO67lARKETJH6WuQVDDlRfj81AJJSswMyMkXTD49wBj5EP004WOY6" crossorigin="anonymous"></script>
<script src ="https://cdnjs.cloudflare.com/ajax/libs/markdown-it/8.4.0/markdown-it.js" crossorigin="anonymous"
></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.9.0-beta/contrib/auto-render.min.js" integrity="sha384-EkJr57fExjeMKAZnlVBuoBoX0EJ4BiDPiAd/JyTzIA65ORu4hna7V6aaq4zsUvJ2" crossorigin="anonymous"></script>
<script src="https://gitcdn.xyz/cdn/goessner/markdown-it-texmath/master/texmath.js" crossorigin="anonymous">
</script>
<link rel="stylesheet" href="https://gitcdn.xyz/cdn/goessner/markdown-it-texmath/master/texmath.css" crossorigin="anonymous">

<script
 src="http://code.jquery.com/jquery-latest.js"></script>
<p><link rel="stylesheet" href="https://gitcdn.xyz/cdn/goessner/markdown-it-texmath/master/texmath.css" crossorigin="anonymous"></p>

<script type="text/javascript">
	$(document).ready(function(){
	$("code").map(function(){
	 renderMathInElement(
           $(this)[0] ,
          {
              delimiters: [
                  {left: "$$", right: "$$", display: true},
                  {left: "\\[", right: "\\]", display: true},
                  {left: "$", right: "$", display: false},
                  {left: "\\(", right: "\\)", display: false}
              ]
          }
      );
	});
	});
</script>

```   

其实我没写过js 代码，但是大致能够看懂，这里的坑有要用jquery ，不然代码中会有语法错误。   
基本思想是 找到markdown 生成的html文件中的 \<code\>标签，然后将其中的内容用renderMathInElement 函数处理就行了。用的是texmath 插件，跟VSCode 上的插件保持一致。  


但是这个问题其实并没有完美解决，生成的 inline 公式在html 中会用一个黑框包起来，看起来不是很美观，这个是应该是主题的问题，应该找到\<code\> 字段的css文件将样式改一下就好了。    

另外一个问题是用了反转义之后 VSCODE 插件上就不能正确预览了。 暂时只能先写公式确定没问题之后再加上 ` 字符了。这个问题感觉我这个前端新手需要花比较大的精力去解决。   



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

