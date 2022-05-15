## FPN
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/FPN/4FPN.pdf)


一篇检测领域较经典的论文，使用了金字塔结构，基于Faster R-CNN做的改进

-Feature Pyramid

文中将其称为特征金字塔，实际做法就是两层特征一融合，举例来说，原文中C2C3C4C5卷积模块输出分别为R2R3R4R5,那么C2的最终输出P2就应该是R2 + R3做上采样（普通的双线性插值）2倍以后的加和，结果经过一个卷积层后作为P2为最终的特征图，再利用得到的P2P3P4P5分别输入到RPN模块和fast RCNN模块里做正常的的计算，同时RPN和fast RCNN后续的模块对于不同融合的特征图都共享权重

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/FPN/1.png)

-多尺度

因为这里的特征金字塔是多尺度的，所以文中并没有对每个尺度的特征图都做所有大小锚框的预测，而是根据越是浅层的特征图，保留的像素信息越多这一情况，对浅层的特征图做小目标预测，对深层的特征图做大目标预测（仔细想想蛮有道理的，有时候图片很糊的话只能看清个大概的轮廓，类似于大目标）

-一些细节

这里提取到特征图然后做锚框的预测还有个问题就是，在融合后的特征图预测到的框应该是对应原图的特征图的哪一级，文中给出了一个公式，我感觉蛮奇怪的，有点不太理解公式为什么这样写，暂时的理解就是，P2对应C2这种，另外的是P5P6对应C5，P6单纯的是想增加一个更大的锚框，等之后看完了faster RCNN的源码再来想想这个问题吧

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/FPN/2.png)
