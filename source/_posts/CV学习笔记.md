---
title: 计算机视觉的学习笔记
data: 2026-03-22
author: 空空杯
math: true
excerpt: 按照大模型推荐的顺序，对经典的和近年的计算机视觉文章进行精读并记录相关知识点
categories: 
  - 芝士,与你分享
---
## 一、[AlexNet](https://dl.acm.org/doi/10.1145/3065386)
- 核心亮点：证明了深层神经网络在图像分类上的潜力，开创性地利用了 GPU 进行大规模并行计算训练，奠定了现代 AI 算力与算法结合的基础。
- 思想方法与操作细节：
  1. 深度学习需要大量数据训练,至少需要上万张图片；[^1]
  2. 模型的全连接层似乎很重要；[^2]
  3. 非线性激活函数能有效加快模型训练速度；[^3]
  4. 重叠池化层(相邻池化层的池化单元大小不一致)能略微提高准确度；[^4]
  5. 在模型参数量较大时(60 million)，防止过拟合的几种方法
     - 数据增强：从已有数据集中随机取像素块进行平移或翻转，或者改变训练图像中的RGB通道强度;[^5]
     - Dropout：随机丢弃神经元(将参数置零)来减少神经元之间复杂的共适应关系。
- AlexNet网络架构图
  
![AlexNet网络架构图](/img/CVLearning/1.png)

&emsp;原文使用了两块GPU来训练模型，一块负责跑图片上半部分，一块跑下半部分，所以其有两层一样的，最后通过全连接层相互通信。

## 二、[ResNet](https://ieeexplore.ieee.org/document/7780459)
- 核心亮点：提出了残差连接（Residual Connection）。通过引入 Skip Connection，解决了极深网络训练中的梯度消失问题。
- 思想方法与操作细节：
  - 模型的网络深度很重要[^6]，但是过深的网络模型存在梯度爆炸/消失的问题；
  - 模型达到一定深度后，增加层数会使模型损失精度，这并不是由过拟合导致的；[^7]
  - 残差网络对于解决梯度问题的关键在于，其保留了layer的输出 $f(x)$和输入的 $x$，同时，可以通过控制 $f(x)$的层数，来轻易优化模型。[^8]
  - 残差片段(Shortcuts)数学公式 $\mathbf{y} = \mathcal{F}(\mathbf{x}, \{W_i\}) + \mathbf{x}$ 当 $\mathcal{F}(\mathbf{x}, \{W_i\})$ 只有一层时，其为线性层。[^9]
- 残差网络片段

![残差网络模型片段](/img/CVLearning/2.png)

### 三、[Faster R-CNN](https://arxiv.org/abs/1506.01497?utm_source=chatgpt.com)
- 核心亮点：引入“锚点”机制、统一框架与交替训练。
- 思想方法与操作细节：
- Faster R-CNN网络架构图
### 补充知识
- 残差向量(residual vectors):残差向量由多层编码器编码构成，其构成方式类似于将泰勒展开以向量的方式进行表示，我们假设原始数据(其为嵌入后的向量数据)为 $f(x)$那么，经过第一层编码器的得到的结果为 $g_1(x)$，其中必然存在一定的误差，我们设误差为 $g_2(x)$，有 $g_2(x) = f(x)-g_1(x)$，如此反复，我们几乎能无损的表示原始数据。
### 脚注
[^1]:[Until recently, datasets of labeled images were rela-tively small—on the order of tens of thousands of images(e.g., NORB,19 Caltech-101/256,8, 10 and CIFAR-10/10014).](https://dl.acm.org/doi/10.1145/3065386)
[^2]:[we found that removing any convolutional layer (each of which contains no more than 1% of the model’s parameters) resulted in inferior performance.](https://dl.acm.org/doi/10.1145/3065386)
[^3]:[In terms of training time with gradient descent, these saturating nonlinearities are much slower than the non-saturating nonlinearity $f(x) = max(0, x)$.](https://dl.acm.org/doi/10.1145/3065386)
[^4]:[This scheme reduces the top-1 and top-5 error rates by 0.4% and 0.3%, respectively, as compared with the non overlapping scheme s = 2, z = 2, which produces output of equivalent dimensions.](https://dl.acm.org/doi/10.1145/3065386)
[^5]:[The first form of data augmentation consists of generating
image translations and horizontal reflections.……The first form of data augmentation consists of generating image translations and horizontal reflections.](https://dl.acm.org/doi/10.1145/3065386)
[^6]:[Recent evidence reveals that network depth is of crucial importance.](https://ieeexplore.ieee.org/document/7780459)
[^7]:[Unexpectedly,such degradation is not caused by overfitting, and adding more layers to a suitably deep model leads to higher training error.](https://ieeexplore.ieee.org/document/7780459)
[^8]:[We show that: 1) Our extremely deep residual nets are easy to optimize, but the counterpart “plain” nets (that simply stack layers) exhibit higher training error when the depth increases; 2)……](https://ieeexplore.ieee.org/document/7780459)
[^9]:[But if $\mathcal{F}$ has only a single layer, Eqn.(1) is similar to a linear layer](https://ieeexplore.ieee.org/document/7780459)