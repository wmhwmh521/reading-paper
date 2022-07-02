## OW-DETR

***

- [原论文]原文太大github传不上来


这篇文章讨论的是目标检测方向的开放世界目标识别问题，所谓开放世界指的就是预测集合中的目标包含训练集中不存在的种类，有些类似分类中的开集识别，即训练过程中不仅要学习识别已经有标签的目标，还要能够自行学习并找到新的目标种类

-整体思路

下图是本文解决开放世界目标检测问题的整体思路，通过拿到不同候选框的query embedding，根据不同embedding做相应的预测，黄色代表已知类，即训练集中包含的类别，紫色代表位未知类，模型通过训练数据自己学习到的未知类，红色代表背景

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/OW-DETR/1.png)


-整体框架

下图是文章的整体框架，在DETR的基础上做改进，主要是分为以下几部分
- Multi-scale Context Encoding
- Attention-driven Pseudo-labeling
- Novelty Classification
- Foreground Objectness

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/OW-DETR/2.png)

-Multi-scale Context Encoding

输入进transformer之前并不是单纯的取CNN的最后一层，而是取多层的特征图然后叠在一起作为transformer的输入

-Attention-driven Pseudo-labeling

这部分内容是文章比较核心的思路，即如何通过模型来做未知类的预测，主要部分和正常的目标检测模型思路相同，通过一个回归器来进行锚框的回归，对于一张图片，假设有K个已知的目标，M个object query，首先通过DDERT方式选出K个和已知类最相近的query（该方式应该是DDETR提出的，并不了解详细的内容），然后在M-K个object query中寻找Ku个未知目标预测分数最高的，这些query标记为未知目标

那么未知目标预测分数如何计算呢？文章给出的思路是通过目标锚框特征图平均值来计算的，通过回归器预测出的锚框，返回到之前的特征图中，找寻特征图激活程度最大的锚框，相对来说就是存在未知目标的区域

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/OW-DETR/3.png)

-Novelty Classification 和 Foreground Objectness

这两块内容其实在文中的实现非常简单，分别都只是一个FFN，
Novelty Classification用于判别是新的种类还是已经有的种类，如果是新的种类则统一都归为Novelty这一类，代表可能是目标
Foreground Objectness用于判别该锚框是前景还是背景
