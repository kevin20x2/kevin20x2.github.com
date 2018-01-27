---
layout: post
title: "Quantized-CNN"
date: 2018-01-27 15:28:16 +0800
comments: true
categories: 
---
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
   // match = /^\$(.*)\$$/.exec($(this).html());
   // if (match){
      //$(this).after("<span class=mathjax_inline>" + match + "</span>");
      //$(this).hide();
     // $(this).replaceWith("<span class=hpl_mathjax_inline>" + $(this).html() + "</span>");
     // MathJax.Hub.Queue(["Typeset",MathJax.Hub,$(this).get(0)]);
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
   // }
	});
	});
      // document.addEventListener("DOMContentLoaded", function() {
       
   // });
</script>
# Quantized Convolutional Neural Networks for Mobile Devices
---
## 摘要:
---    
* 提出了量化 CNN，同时**加速计算** 和 **压缩模型**
* 同时量化卷积层的kernel 和 全连接层的参数。 
* ILSVRC-12 上做实验，在1%的准确度损失下，得到了4-6x 加速比和15-20的压缩比  

<!--more-->
## 简介  
---
1. 说明了CNN 在图像分类领域的成功。
2. 引出，虽然在GPU平台上速度很快，但是在普通的手机设备上，因为有限的算力和内存容量CNN的模型大小和计算速度都不尽人意。
3. 说明CNN中，**卷积层**的计算是最消耗算力的地方，而**全连接层**是参数最多的层。
4. 简单了提了一下相关工作，说明其他人的工作要么是着重于提高计算速度，要么是着重于压缩。很少有人能够做到同时加速和压缩。
5. 引出Q-CNN，同时加速和压缩CNN。网络的参数量化之后，卷积层和全连接层可以通过 approximate inner product computation来有效估计。
6. 为了减小误差同时量化CNN中的层，提出了一个训练机制。
7. 说明Q-CNN 计算前向传播的速度很快，并且减少了内存消耗。  
8. 说明Q-CNN在两个测试集上做过测试，MNIST 和 ILSVRC-12，对于MNIST ， 获得了12x的压缩率，并且比有些基准获得更少了accuracy loss。 对于 ILSVRC-12，在**AlexNet** ，**CaffeNet**，**CNN-S**，**VGG-16**上做过测试。Q-CNN 获得了**4x**的加速比和**15x**的压缩比，损失了不到**1%**的**top-5**精度。
9. 并且实现了移动设备上的quantized CNN 模型。  

---
## 准备工作  
---
文中的基本想法是: 不管是全连接层还是卷积层的计算，都能够看成**向量内积**的和。  

考虑卷积层的输入 `$S\in\mathbb R^{d_s\times d_s\times C_s}$` 和输出 `$T\in \mathbb R^{d_t\times d_t\times C_t}$ , $d_s,d_t$` 是输入，输出特征图的大小。 `$C_s,C_t$`是特征图的通道。于是输出的特征图中，第ct 个通道中，位置 `$p_t$`上的输出为:

`$$T_{p_t}(c_t) = \sum_{(p_k,p_s)} <W_{c_t,p_k},S_{p_s}>$$`

其中 `$W_{c_t}\in \mathbb R^{d_k\times d_k\times C_s}$`是第ct个卷积核，`$d_k$`是kernel size。 
这里很好理解，因为卷积运算能够转换成矩阵乘法，实际上也就是多个向量乘积的和。
全连接层本身就可以看作向量的内积，这里不详细说。   

文中是考虑了每一个向量的内积和: 假设要求向量` $x,y \in \mathbb R^D$`的内积。先将x,y 都切分成 M 个 子向量，用 `$x^{(m)},y^{(m)}$` 表示。 然后，每一个`$x^{(m)}$` 被一个sub-codebook 中的第m个sub-codeword 量化，于是有 :

`$$<y,x> = \sum_{m}<y^{(m)},x^{(m)}>  \approx \sum_m<y^{(m)},c^{m}_{k_m}>$$ ` 

假如将每个`$y^{(m)}$`和转换后的sub-codeword 的子内积提前计算出来的话，就可以避免乘法运算，并且最后只有M次加法运算。   
这种方法可以量化卷积层和全连接层，并且减少运算。  


---
## Q-CNN
---    
### 量化全连接层  
![figure_2](https://github.com/kevin20x2/kevin20x2.github.com/blob/master/images/Q-CNN-2.png?raw=true)

设W 是权重矩阵，首先要生成大小为m的codebook， 于是用k-means 聚类算法，将W 按照行数分成m个子矩阵，然后聚m类。得到codebooks。  
计算的时候，将输入的input 向量也拆分成m个子向量，然后每一个子向量与codebooks里面的每一个向量相乘，得到预先计算的结果。  
然后将不同的结果相加得到输出的结果。   
算法将原来的`$O(C_sC_t)$`的时间复杂度优化至 `$O(C_K+C_tM)$`。



### 量化卷积层    
卷积层的量化方式跟全连接层类似，权重的切分方式是每个kernel 级别的切分。然后也是用k-means 聚类算法，计算出codebooks，算出预先计算的结果，简化计算。



---
## 带错误纠正的量化   
---
说明了只有上面的量化是不够的，主要有两个缺点: 
1. 最小化参数的量化误差并不一定就能给出比较好的分类准确度。
2. 每一层的量化是独立的，因此每一层的量化误差可能累积起来，导致最终的结果不好。

### **全连接层的错误纠正**  
错误纠正的思路都是类似的，因为没有量化的输出结果是最精准的结果，因此，量化后的结果只要尽量跟原始结果相等就行了。剩下的就是公式化的描述和量化参数的更新方式:   

`$$min_{D^{(m)},B^{(m)}} \sum_n \|T_n - \sum_m(D^{(m)}B^{(m)})^TS^{(m)}_n\|^2_F$$ `   

其中Sn 是第n个图像$In$ 的输入，Tn是原始的输出。D 是subcodebooks向量， B是类似与开关的向量，$B^{(m)} \in \{0,1\}^{K \times C_t}$ ，标识选择codebooks中的word 来进行量化。最小化上述误差值是错误纠正的目标。   

上面的公式是总体的目标，具体到分割成m个的，每一个的子空间的误差表示为:

`$$R^{(m)}_n = T_n-\sum_{m'\ne m}(D^{(m')}B^{(m')})^TS^{(m')}_n$$ ` 

也就是正确输出的值，减去所有其他子层输出的和。

#### 更新 D  
当固定B 的时候，对于每一个子空间，变成一个最小2乘问题。

#### 更新 B   
当 D 固定的时候，会发现最小值通过枚举就能得到。   


### **卷积层的错误纠正**  
思路是类似的，全连接层的输出向量变成了输出矩阵，因此需要加一维pt 表示位置。 文中说可以用block coordinate descent。
### **多层的错误纠正**   
这里主要说多个层数之间的输入和输出的问题，文中是假定前面一层已经经过量化好的，也就是说该层的输入是前一层量化后的输出不变化，然后这一层的输出是由该输入，用原始方法算出来的输入来进行纠正的。说了那么多也就是确定跟想象的一致。   

文中提到了可以用反向传播算法来更新量化值，但是由于太难，于是就没有做。 

## 总结   
后面的实验部分没有看，总的来说这个量化思路感觉还是比较好的，虽然没有用到反向传播算法来更新。   



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