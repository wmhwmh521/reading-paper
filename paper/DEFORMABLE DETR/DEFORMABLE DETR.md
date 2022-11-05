## DEFORMABLE DETR
***

DETR后续比较标准的工作，几乎是所有文章的对比实验

文中首先提出DETR的缺陷
    1.收敛速度慢
    2.对小目标样本检测效果差
    3.计算随着像素数量指数级增加

回顾了一下attention的具体公示操作，从embedding qkv开始到多头注意力结束

文章整体改动在encoder部分，decoder部分改动较少，整个文章是对比标准的DETR的transformer部分改改进，从时间复杂度方面和可解释性方面

-改动

    1.主要更改的是注意力的计算方式，从transformer 缩放点积注意力改成了自己重新设计的deformable attention
    2.在QKV的V部分做了一些变化
    3.加入了多尺度策略

-encoder

将encoder整体大改，改成了CNN类似的思路，读了几篇论文发现现在的改变思路都是用CNN代替transformer，因为transformer做特征提取实在太慢随像素数量指数级提升

    1. 与transformer encoder完全不同，但思路有一些保留，比如QKV
    2. 将query表示为2部分，Reference Point Pq和Query Feature Zq，Pq表示在原始特征图中的某个像素位置，Zq则用于预测后续的一些偏移offset和attention权重
    3. offset模块：Query Feature Zq首先会经过一个linear层，得出offset，该offset是一个二维坐标的偏移量，会被加到Pq上以调整Pq的位置，Pq位置也可以被认为box的初始预测位置，通过offset不断调整以接近期望的位置，同时这部分还加入了多头的思路，即拥有m个head，每个head有K组offset，单个head的output参数为2K，对应偏移量的xy，m个head则为2mK
    4. Attention Weights模块：不再使用QKV的缩放点积来获取attention权重，而是仍然通过Query Feature Zq来预测权重，需要参数mK
    5.加入多尺度操作，即之前是只对一个特征层采样，现在对多个特征层同时做这一套操作

PS.读完这一篇感觉想要解决DETR速度慢的问题只能从时间复杂度的角度上考虑，即舍弃传统的self attention操作，因为像素太多了，swin transformer也是这么做的