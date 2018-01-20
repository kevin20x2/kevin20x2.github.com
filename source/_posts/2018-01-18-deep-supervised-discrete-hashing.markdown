---
layout: post
title: "(论文整理1)Deep_Supervised_Discrete_hashing"
date: 2018-01-18 18:24:06 +0800
comments: true
categories: hash Quantization NIPS_2017
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


# **摘要**
随着图片和视频数据的增长，哈希被大规模的使用在图片和视频的研究中。得益于深度学习，**深度哈希方法**(deep hasing methods)在图像检索中已经取得了不错的效果。   
但是，之前的深度哈希方法有一些限制(如：语义信息没有充分利用)。在本paper中，我们提出了一个 **深度监督离散哈希算法**(Deep supervised discrete hashing)，跟据"学习到的二进制数据对于分类效果应该很好" 这个假设。  
在同一个流框架中，同时使用 **标签信息**和**分类信息**作为一对数据来学习哈希代码。我们强制**使最后一层直接输出二进制数据**，之前很少有人做过这方面的深度哈希。因为哈希代码离散的天性，可以使用一个交替化最小方法来优化目标函数。实验表明，我们的方法比最先进的方法还要更好。

<!--more-->

# **简介**

因为网络上图像和视频数据的快速增长，**哈希(hashing)** 近年来吸引到了很多的注意力。因为其**低计算消耗**和**高存储效率**的特性，已经成为最流行的技术之一。简单来讲，哈希是用来编码高纬度数据到二进制编码(binary codes)中，同时保持图像和数据的相似性的(similarity)。现有的哈希方法大体可以分为两种: 数据独立的方法(data independent methods)，和数据相关的方法(data dependent methods)。  

### **数据独立的方法**  
依赖于 **随机投影**(random projections) 来构建哈希函数。**局部敏感哈希**(LSH) 是其中代表之一。它使用随机线性投影来将相邻的数据映射到相似的二进制编码中。LSH在图像检索中被大规模使用。为了泛化LSH来适应任意的内核函数(kernel function), **核心局部敏感哈希**(KLSH)被提出来用于处理高维度的核心化的数据。也有其他LSH的变体，如super-bit LSH，non-metric LSH 。但是 **数据独立的哈希方法的限制是没有使用训练数据**。训练低效而且需要很长的hash code来获取高准确度。因为数据独立哈希方法的这些限制，最近的哈希方法尝试利用各种机器学习的技术在给定的数据集来学习更有效的哈希函数。   


### **数据相关的方法**  
指的是使用训练数据来学习哈希函数。进一步可以分为 有监督的哈希方法和无监督的哈希方法。无监督的方法根据某种距离度量来检索相邻数据。**迭代量化**(ITQ)是其中一种有代表性的无监督哈希方法，其 根据给定的训练样本，通过**迭代投影**(iterative projection)和阈值处理(thresholding)来优化投影矩阵。  
为了利用数据样本的语义表情，有如下有监督的哈希算法:  

* KSH (supervised Hashing with Kernels)  
通过最小化 相似对之间的Hamming 距离，同时最大化不相似对之间的距离，来学习hash code。  
* BRE (Binary Reconstruction Embedding)  
利用最小化Hamming 空间的初始距离和重构距离之间的错误。  
* OPH (Order Preserving Hashing)  
维护根据语义标签计算出来的 监督的 raking list 信息来学习哈希 code。
* SDH (Superived Discrete Hashing)  
直接通过**离散循环坐标下降法**(discrete cyclic coordinate descent method)优化hash code. 


### **基于深度学习的hash 方法** :   
* CNNH (Convolutional Neural Network Hashing)   
* NINH (Network in Network Hashing)
* DSRH (Deep Semantic Ranking Hashing)
* ADMM (ADMM)    

虽然基于深度学习的方法在图像检索中取得了很大的成就，但是仍然有一些限制(eg. 没有充分利用语义信息) 。 相关工作尝试将学习流程分为两个多任务学习框架流。  
hash stream 用来学习哈希函数，classification stream 被用来挖掘语义信息。  
虽然这种框架能够提升检索表现，但是 分类流只用来学习图像表示 (image representations) ，对于哈希函数没有直接影响。   
在这篇paper 中，我们使用CNN来**同时学习图像表示和哈希函数** 。CNN的最后一层基于**标签数据和分类数据对**直接输出2进制编码。    

### **贡献:**  
1. 我们方法的最后一层被限制为直接输出二进制代码。 学习二进制代码以保持相似性关系并同时保持标签一致。第一个在一个流框架中同时使用标签和分类数据来学习hash code.  
2.  因为哈希代码离散的天性，可以使用一个交替化最小方法来优化目标函数。  
3.  实验表明，我们的方法比最先进的方法还要更好。  


## **深度监督离散哈希(Deep superised discrete hashing)**  

### 问题定义
 给定N 个图像样本 `\(X = \{x_{i}\}^{N}_{i=1} \in \mathbb R^{d \times N}\) `,哈希编码是要学习一个K比特2进制编码集合 `\(B \in \{-1,1\}^{K \times N}\)`,其中第i列的 `\(b_i \in \{-1,1\}^K\) 表示第i个样本 \(x_i\)`的2进制编码。  
 
 2进制编码是由hash 函数 `$h(.)$`来生成的。哈希函数可以写成 `$h(x_i) = [h_1(x_i),...,h_c(x_i)]$` 对于数据样本`$x_i$` ,其hash code 可以表示为 `$b_i = h(x_i) = [h_1(x_i),..h_c(x_i)]$`，通常来说，哈希化是学习一个哈希函数来将图片样本处理为2进制编码。   

### 相似度衡量 (Similarity meaure)  
 在监督哈希化中，标签信息被表示为 `$Y = \{y_{i}\}^{N}_{i=1}\in \mathbb R^{c \times N}$` ,其中`$y_i\in\{0,1\}^c$`与样本 `$x_i$`对应，c是分类的个数。 **一个样本可能属于多个分类集合**。  
 给定 语义标签信息，配对标签信息(pairwise label information)可以写成 : `$S = \{s_{ij}\},s_{i,j}\in\{0,1\}$` 。 当`$s_{ij}=1$`时说明 `$x_i$`和 `$x_j$` 相似，反之等于0时不相似。  
 Hamming 距离 可以由如下公式表示:  

`$$ dist_{H}(b_i,b_j) = \frac{1}{2}(K -<b_i,b_j> ) $$`  
其中 $<b_i,b_j>$表示 两者的内积。 如果内积很小，那么Hamming距离就很大，反之亦然。因此，不同的hash code 的内积 可以用来标识其相似度。   


给定 配对相似度关系 `$S=\{s_{i,j}\}$`， 其 **最大后验估计**(MAP estimation) 可以表示为 :

 `$$p(B|S) \propto p(S|B)p(B) = \prod_{s_{ij}\in S}p(s_{ij}|B)p(B) $$`  

 其中 p(S|B) 是相似函数，p(B) 是先验分布。对于每一对图像，` $p(s_{ij}|B)$` 是给定hash code B 的`$s_{ij}$`条件概率,定义如下: 

 `$$p(s_{ij}|B) = \begin{cases} \sigma(\Phi_{ij}), s_{ij} =1\\ 1- \sigma(\Phi_{ij}), s_{ij} =0\end{cases} $$ ` 

 其中 `$\sigma(x) = 1/(1 + e^{-x})$` 是sigmmoid 激活函数，`$\Phi_{ij} = \frac{1}{2}<b_i,b_j>$`上述公式说明，内积越大，`$b_i$和$b_j$` 应该被分类成相似的，反之亦然，因此对于hash code 来说上述公式是reasonable 的。  


 ---
### 注:**最大后验估计 MAP**  
 **文中没有详细解释最大后验估计，这里参照[这里](https://www.cnblogs.com/easoncheng/archive/2012/11/08/2760675.html) 的例子来说明。  
 举例来说：**

　　假设有五个袋子，各袋中都有无限量的饼干(樱桃口味或柠檬口味)，已知五个袋子中两种口味的比例分别是  
 
  樱桃|柠檬  
-|    -
樱桃 100%| 柠檬 0%
樱桃 75% | 柠檬 25%  
樱桃 50% | 柠檬 50%  
樱桃 25% | 柠檬 75%  
 樱桃 0%|柠檬 100%

　　如果只有如上所述条件，那问从同一个袋子中连续拿到2个柠檬饼干，那么这个袋子最有可能是上述五个的哪一个？

我们首先采用最大似然估计来解这个问题，写出似然函数。假设从袋子中能拿出柠檬饼干的概率为p(我们通过这个概率p来确定是从哪个袋子中拿出来的)，则似然函数可以写作

`$$p(2lemon|packet) = p^2$$`  

　　

　　由于p的取值是一个离散值，即上面描述中的0,25%，50%，75%，1。我们只需要评估一下这五个值哪个值使得似然函数最大即可，得到为袋子5。这里便是最大似然估计的结果。

上述最大似然估计有一个问题，就是没有考虑到模型本身的概率分布，下面我们扩展这个饼干的问题。

假设拿到袋子1或5的机率都是0.1，拿到2或4的机率都是0.2，拿到3的机率是0.4，那同样上述问题的答案呢？这个时候就变MAP了。我们根据公式


`$$MAP = p^2 \times g$$`  
　　

写出我们的MAP函数。

　　

根据题意的描述可知，p的取值分别为0,25%，50%，75%，1，g的取值分别为0.1，0.2,0.4,0.2,0.1.分别计算出MAP函数的结果为：0,0.0125,0.125,0.28125,0.1.由上可知，通过MAP估计可得结果是从第四个袋子中取得的最高。


 ---


## Loss function  
我们采用现有CNN的优势来学习hash function，为了与其他deep hashing methods 相比较，选择 CNN-F 网络架构来作为算法的基本组件。具体来说，我们分别有两个共享权值的CNN来学习hash function，样本对被当作这两个CNN的输入。 CNN 模型有5个卷积层和2两个全连接层。最后一层的神经元g额数等于hash code 的个数。  
考虑到相似度衡量，以下loss function 被用来学习hash code : 

`$$J = -log p(S|B) = - \sum_{s_{ij}\in S} log p(s_{ij}|B) = -\sum_{s_{ij}\in S}(s_{ij}\Phi_{ij} -log(1+e^{\Phi_{ij}}))$$`  

上述公式 使得两个相似样本的Hamming 距离尽可能小，同时使得两个不相似样本的距离尽可能大。  

尽管标签对信息被用来学习hash function，但是标签信息并没有被充分利用。大部分之前的工作在两个**多任务学习框架**中利用标签信息。 我们基本假定学习2进制编码对于分类很理想。为直接利用标签信息，我们期望学习的二进制编码对于联合学习的线性分类器是最优的。   
我们使用一个简单的线性分类器来给学习完的的2进制编码和标签信息 建模:  

`$$ Y = W^TB$$`  

其中 `$W = [w_1,w_2,...,w_k]$` 是分类器的权重， `$Y = [y_1,y_2,...,y_N]$`是实际的标签向量。 loss function 可以写成 : 

`$$Q = L(Y,W^Tb_i) + \lambda \left \| W\right \|^2_F $$  `

L 是los function ，`$\lambda$`是正则化参数， `$\left \| W\right \|_F$` 是矩阵的F范式。结合前面的相似性公式,并且将loss function 看作经典的线性 `$l_2$` 损失函数。最后的公式可以写作 : 

`$$F = - \sum_{s_{ij}\in S}(s_{ij}\Phi_{ij} -log(1+e^{\Phi_{ij}})) + \mu \sum^{N}_{i=1} \left\|y_i - W^Tb_i \right\|^2_2 + \nu \left\|W\right\|^2_F$$`  

最终的公式会使标签对生成的2进制编码的相似度尽可能的大。   


## 优化 (Optimization)  

上面公式的最小化是一个离散优化问题，通常直接优化是比较困难的。
在训练阶段，在最后一个全连接层，sigmoid 或者tanh 激活函数被用来取代RelU 函数，并且hash code 的结果为连续的输出.在测试阶段，会在连续的output上使用阈函数。这种方法的一个缺点是比较慢，除此之外，还会存在大量的量化错误。sign fuction 强制使ouput变成2进制变量，但是sign 函数不可微分，因此很难用反向传播算法来计算loss function。

因为 欧几里得空间和Hamming 空间的矛盾，如果完全忽略二进制约束，则会导致次优hash code。我们认为保持 2进制的编码的特性很重要。因此我们在公式中添加了辅助变量:   

`$$F = - \sum_{s_{ij}\in S}(s_{ij}\psi_{ij} -log(1+e^{\psi_{ij}})) + \mu \sum^{N}_{i=1} \left\|y_i - W^Tb_i \right\|^2_2 + \nu \left\|W\right\|^2_F,$$ `     
`$$s.t.  b_i = sgn(h_i),h_i \in \mathbb R^{K \times 1},(i = 1,...,N)$$  ` 

其中 `$\psi_{ij} = \frac{1}{2} h^T_j h_i$` 可以看作是最后一个全连接层的输出，可以表示为 :

`$$h_i = M^T \Theta(x_i;\theta) + n$$  `

其中 $\theta$ 表示最后一个全连接层 **之前** 的前面层的 参数，`$M\in\mathbb R^{4096\times K}$` 表示权重矩阵， `$n\in\mathbb R^{K\times1}$` 是偏置项。
    根据拉格朗日乘数法公式可以写成: 

`$$F = - \sum_{s_{ij}\in S}(s_{ij}\psi_{ij} -log(1+e^{\psi_{ij}})) + \mu \sum^{N}_{i=1} \|y_i - W^Tb_i \|^2_2 + \nu \left\|W\right\|^2_F, +\eta\sum^{N}_{i=1}\|b_i-sgn(h_i)\|^2_2$$`    
`$$s.t.  b_i = sgn(h_i),h_i \in \mathbb R^{K \times 1},(i = 1,...,N)$$ ` 

其中 `$\eta$`是拉格朗日乘数. 
关于拉格朗日乘子法，见[这里](https://www.zhihu.com/question/38586401) 

最后一项其实是用来衡量最后一个全连接层的 约束违规。(constraint violation)   

引入辅助变量的好处是我们可以将上面的公式变成两个子优化问题，以便可以用交替最小化方法迭代解决。   

首先 ， 固定` $b_i,W$`( 哈希结果 , 分类器权重) ，我们有  :

`$$\frac{\partial F}{\partial M} = -\frac{1}{2}\sum_{j:s_{ij}\in S}(s_{ij}-\frac{e^{\psi_{ij}}}{1+e^{\psi_{ij}}})h_j-\frac{1}{2}\sum_{j:s_{ji}\in S}(s_{ji}-\frac{e^{\psi_{ji}}}{1+e^{\psi_{ji}}})h_j - 2\eta(b_i-h_i)$$  `

于是，可以这样更新参数 `$M,n,\Theta$ `  

`$$\frac{\partial F}{\partial M} = \Theta(x_i;\theta)(\frac{\partial F}{\partial h_i})^T,\frac{\partial F}{\partial n} = \frac{\partial F}{\partial h_i} , \frac{\partial F}{\partial \Theta(x_i;\theta)}= M\frac{\partial F}{\partial h_i} $$ `  

第二步，当固定` $M,n,\Theta ,b_i$`()时，我们可以求解W :   

`$$F = \mu\sum^N_{i=1} \|y_i - W^Tb_i\|^2_2+\nu\|W\|^2_F$$  `

上式是最小平方问题，有固定解法:

`$$W = (BB^T + \frac{\nu}{\mu}I)^{-1}B^TY$$ ` 
其中 `$B = \{b_i\}^N_{i=1} \in \{-1,1\}^{K \times N} , Y = \{y_u\}^N_{i=1}\in\mathbb R^{C\times N}$ `

最后，当固定 `$M,n,\Theta , W$`有：

`$$F = \mu\sum^N_{i=1}\|y_i - W^Tb_i\|^2_2 + \nu\|W\|^2_F+\eta\sum^N_{i=1}\|b_i-h_i\|^2_2$$  `

这里使用离**散循环坐标下降法**(discrete cyclic coordinate descend method)来来一行一行求解B ：  

`$$min_B \|W^TB\|^2 -2Tr(P), s.t. B\in\{-1,1\}^{K \times N}$$ `  


其中` $P = WY + \frac{\eta}{\mu}H$`,令 `$x^T$` 为B的 第 k(k = 1,...,K) 行,`$B_1$` 是 B去除` $x^T$ `的矩阵，`$p^T$` 是矩阵P的第k 列 ,`$P_1$` 是矩阵P 出去p的矩阵，`$w^T$` 是`$W$`的第k列，`$W_1$` 是W除去w的矩阵，有:  

`$$x = sgn(p-B^T_1W_1w)$$ ` 

容易看出hash code 的每一位是由提前学习的 K-1 bit `$B_1$` 计算出来的。直到算法收敛，迭代着更新每一位。


## 实验   

1. 实验设置
* 实验使用两个数据集 ： CIFAR-10 ， NUS-WIDE.   
* 对比实验，分成两组 ， 传统hash 方法和 deep hash 方法。 
* 非监督的hashing 方法 ，包括 SH，ITQ ，监督hash 方法包括: SPLH,KSH,FastH,LFH,SDH。    
* 深度hash 算法包括 DQN,DHN,CNNH,NINH,DSRH,DSCH,DRCSH,DPSH,DTSH,VDSH   

2. 实验结论   
比最新的好 

(这个地方太繁杂，建议直接看原文)    

体会 :   

* 本文所说的hash 并不是deep compression ，hashNet 那种为了减少权重和压缩模型的hash。方向在于hash 输入输出特征图。 根据标签中相同的语义将 相似的图像数据hash成 相似的hash code。意义是 提高 图像检索的准确度。  
* 其中设计到了很多数学方法和公式 : 概率论中 的 MAP ，机器学习常见的BP，以及正则化方法，还有高数中的拉格朗日乘子法。(让我这个数学弱渣情何以堪) 很多地方其实只能看个大概，要自己做这个研究数学功底肯定是要比较深厚的。  
* 本文也其实体现了图像检索的一个基本思想 : 将相似的图片 (语义相似，标签相似) ,通过CNN学习hash 函数，hash 成相似的2进制code ，以此在查询的时候输出最相似的图片，完成查询流程。





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