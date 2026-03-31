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
  2. 模型的全连接层在图片识别过程中似乎很重要；[^2]
  3. 非线性激活函数(ReLU)能有效加快模型训练速度；[^3]
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
  1. 模型的网络深度很重要[^6]，但是过深的网络模型存在梯度爆炸/消失的问题；
  2. 模型达到一定深度后，增加层数会使模型损失精度，这并不是由过拟合导致的；[^7]
  3. 残差网络对于解决梯度问题的关键在于，其保留了layer的输出 $f(x)$和输入的 $x$，同时，可以通过控制 $f(x)$的层数，来轻易优化模型；[^8]
  4. 残差片段(Shortcuts)数学公式 $\mathbf{y} = \mathcal{F}(\mathbf{x}, \{W_i\}) + \mathbf{x}$ 当 $\mathcal{F}(\mathbf{x}, \{W_i\})$ 只有一层时，其为线性层。[^9]
- 残差网络片段

![残差网络模型片段](/img/CVLearning/2.png)

## 三、[Faster R-CNN](https://ieeexplore.ieee.org/document/7485869)
- 核心亮点：引入“锚点”机制、统一框架与交替训练、实现了实时区域检测
- 思想方法与操作细节：
  1. 区域提取方法通常依赖于低成本特征和经济的推理方案，主要用的方法是选择性搜索；[^10]
  2. 区域提议网络(RPN)类似于一种全连接层(FCN)，能实现端到端的训练；[^11]
  3. 整个网络架构用卷积层来提取特征，随后用RPN来对特征图进行区域提议(绘制锚框)，最后使用分类器来对区域中的特征来进行分类，简单来说，就是 RPN 告诉 RCNN 该往哪看，相较于Fast RCNN，其加快了RoI的寻找速度；[^12]
  4. 用锚点金字塔来处理网络，即在一个锚点上绘制多种锚框，然后通过IoU来选取框来进行绘制(其实就是Yolo锚框的画法)。这样能避免因为图像金字塔的多重卷积导致的时间消耗；[^13]
  5. 使用交替训练的方式来使两种网络来共享卷积层：用预训练好的RPN生成的提议，然后基于提议来让Fast R-CNN来训练一个独立的检测网络，随后用该检测网络来初始化训练RPN，但固定共享卷积层，仅微调 RPN 独有的层。最后，保持共享卷积层固定，微调 Fast R-CNN 独有的层。[^14]
- Faster R-CNN网络架构图

![Faster R-CNN](/img/CVLearning/5.png)

&emsp;网络的核心在与，共享卷积层来减少运算量，同时通过交替训练来确保两个模型的“数据共享”。再则就是只训练“感兴趣”的区域。

## 四、[U-Net](https://arxiv.org/abs/1505.04597v1)
- 核心亮点：分割领域的代表作、编码器—解码器、跳跃连接、像素级预测
- 思想方法与操作细节：
  1. 追求高精度识别/提高输出分辨率，可以尝试用上采样算子(转置卷积)代替池化算子；[^15]
  2. 语义分割时，如果输入图片边界存在信息缺失，可以用镜像复制的方法来处理这一问题；[^16]
  3. 为了实现对图片中的目标进行定位，还需要将下采样过程中产生的高分辨率特征与上采样的输出进行拼接，减少信息损失；[^17]
  4. 过于靠近的物体可以使用加权损失的方法来进行细致分割；[^18]
- U-Net网络架构图

![U-Net网络架构图](/img/CVLearning/7.png)

&emsp;U-Net网络模型算是广义上的“全卷积模型”，整个网络的粗糙理解可以是：压缩(提取特征) -> 信息传递 -> 解压缩(获得分割图像)

## 补充知识
- 残差向量(residual vectors):残差向量由多层编码器编码构成，其构成方式类似于将泰勒展开以向量的方式进行表示，我们假设原始数据(其为嵌入后的向量数据)为 $f(x)$那么，经过第一层编码器的得到的结果为 $g_1(x)$，其中必然存在一定的误差，我们设误差为 $g_2(x)$，有 $g_2(x) = f(x)-g_1(x)$，如此反复，我们几乎能无损的表示原始数据。
- 全卷积网络(FCN):相较于传统的卷积网络CNN，FCN不限制图片的宽高，将全连接层全部替换为了卷积层，如此一来，输出的结果将不再是"标签"，而是一张压缩过后的"热图"，通过上采样还原，能得到像素级的图像分割，常用于语义分割，示例图如下图所示：

![FCN示意图](/img/CVLearning/3.png)

- [Fast R-CNN](https://arxiv.org/abs/1504.08083v2)：与传统的RCNN相比，Fast RCNN用RoI池化层取代了图像分离金字塔结构，同时用两个并联的全连接层取代了原来的单个全连接层来加速运算，其示意图如下图所示：

![Fast R-CNN结构图](/img/CVLearning/4.png)

- 如何理解卷积后通道数增加：当我们用一个卷积核去卷积图像时，我们会获得一个二维的矩阵，我们可以把他称为特征图(feature map)，显然一张特征图并不能很好的分辨图片中的信息，因此我们改变卷积核内部的偏置，设计某种特征的卷积核，然后再对图片进行一次卷积，再次得到一个特征图。如此往复，我们能得到一个 $n*n*x$ 的结果 $x$ 即为卷积核的数量。
- 上卷积(up-convolution)/ 转置卷积(Transposed Convolution):卷积的逆过程，计算步骤如下
  
![上采样方法](/img/CVLearning/6.png)

## 脚注
[^1]:[Until recently, datasets of labeled images were rela-tively small—on the order of tens of thousands of images(e.g., NORB,19 Caltech-101/256,8, 10 and CIFAR-10/10014).](https://dl.acm.org/doi/10.1145/3065386)
[^2]:[we found that removing any convolutional layer (each of which contains no more than 1% of the model’s parameters) resulted in inferior performance.](https://dl.acm.org/doi/10.1145/3065386)
[^3]:[In terms of training time with gradient descent, these saturating nonlinearities are much slower than the non-saturating nonlinearity $f(x) = max(0, x)$.](https://dl.acm.org/doi/10.1145/3065386)
[^4]:[This scheme reduces the top-1 and top-5 error rates by 0.4% and 0.3%, respectively, as compared with the non overlapping scheme s = 2, z = 2, which produces output of equivalent dimensions.](https://dl.acm.org/doi/10.1145/3065386)
[^5]:[The first form of data augmentation consists of generating image translations and horizontal reflections.……The first form of data augmentation consists of generating image translations and horizontal reflections.](https://dl.acm.org/doi/10.1145/3065386)
[^6]:[Recent evidence reveals that network depth is of crucial importance.](https://ieeexplore.ieee.org/document/7780459)
[^7]:[Unexpectedly,such degradation is not caused by overfitting, and adding more layers to a suitably deep model leads to higher training error.](https://ieeexplore.ieee.org/document/7780459)
[^8]:[We show that: 1) Our extremely deep residual nets are easy to optimize, but the counterpart “plain” nets (that simply stack layers) exhibit higher training error when the depth increases; 2)……](https://ieeexplore.ieee.org/document/7780459)
[^9]:[But if $\mathcal{F}$ has only a single layer, Eqn.(1) is similar to a linear layer](https://ieeexplore.ieee.org/document/7780459)
[^10]:[Region proposal methods typically rely on inexpensive features and economical inference schemes.Selective Search……](https://ieeexplore.ieee.org/document/7485869)
[^11]:[The RPN is thus a kind of fully convolutional network (FCN) [7] and can be trained end-to-end specifically for the task for generating detection proposals.](https://ieeexplore.ieee.org/document/7485869)
[^12]:[Using the recently popular terminology of neural networks with ‘attention’ [31] mechanisms, the RPN module tells the Fast R-CNN module where to look.](https://ieeexplore.ieee.org/document/7485869)
[^13]:[As a comparison, our anchor-based method is built on a pyramid of anchors, which is more cost-efficient](https://ieeexplore.ieee.org/document/7485869)
[^14]:[In this paper, we adopt a pragmatic 4-step training algorithm to learn shared features via alternating optimization](https://ieeexplore.ieee.org/document/7485869)
[^15]:[The main idea in [9] is to supplement a usual contracting network by successive layers, where pooling operators are replaced by upsampling operators.](https://arxiv.org/abs/1505.04597v1)
[^16]:[To predict the pixels in the border region of the image, the missing context is extrapolated by mirroring the input image.](https://arxiv.org/abs/1505.04597v1)
[^17]:[In order to localize, high resolution features from the contracting path are combined with the upsampled output.](https://arxiv.org/abs/1505.04597v1)
[^18]:[To this end, we propose the use of a weighted loss, where the separating background labels between touching cells obtain a large weight in the loss function.](https://arxiv.org/abs/1505.04597v1)