## SPARSE DETR 2022 ICLR
***

这篇文章主要突出的侧重点是LEARNABLE SPARSITY，意思是从transformer提取出的token中，寻找对object detection最有用的那些token，举例子来说就是一张图片被分为9分，每一份都是一个token，此时可以寻找哪一个token对最终预测结果最有用，对其他token做mask掩模，这是我的一种理解；另外一种理解就是每个单独的toknen之间要做QKV操作，这样考虑每个单独token中哪些部分更加有效果，找到这些部分并对其他的位置做mask掩模，是第二种理解

-Attention complexity

从注意力复杂度的角度来看DETER，原版DERT是QK全部计算，Deformable DETR是稀疏计算，Sparse DETR则更加稀疏，选出相对来说最有用的部分，如何选择的评分函数是关键

-Scoring Network

构建了一个评分网络来确定哪些部分较为有效，该网络只是简单的几个线性层做叠加，关键的部分是如何训练该网络，因为如果网络效果很差，很可能在初始训练阶段找不到或者舍弃了比较关键的一些token，导致训练失败

训练方法：
    1. 取得encoder输出，将其输入到decoder中得到结果output
    2. 将input输入到encoder之前先输入到scoring Network，对其做评分，评出top K个最有效的token位置
    2. 通过output对encoder输出做CAM，找出CAM中激活程度最大的部分，对其展平之后和scoring Network的output做二元交叉熵作为loss来训练


-Auxiliary Loss

文中还提及了一个辅助损失，提到在DETR及其变体中，大多只对decoder部分做Auxiliary Loss，原因是decoder的query大部分只有100或者300，这让Auxiliary Loss不会消耗太多的计算量，同时也能提高性能

- encoder Auxiliary Loss

由于本文在encoder部分增加了scoring network去选择哪些token更加有效，在encoder部分就已经主动滤除了很多token，所以也可以使用Auxiliary Loss在encoder部分，以提高整体的性能！

PS. 这个Auxiliary Loss我在DETR源码里看到过，但是还不了解具体是什么loss，之前在网上查了一下也就是多个正则项之类的，怎么实现如果有需要还要具体来看

PS. 总体来看这篇文章给我的这个反馈机制的思路很强，同时预选择token这种思路也可以参考，但是Auxiliary Loss如何用或者说重要性这个还不太清楚