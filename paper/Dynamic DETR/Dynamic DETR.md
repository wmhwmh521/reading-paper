## Dynamic DETR 2021 ICCV
***

文中提到DETR的瓶颈有2个，一个是对小目标检测效果较差，一个是收敛速度慢

    On one hand,
    the input resolution of features maps is limited in the native
    Transformer as feature encoder, since the complexity of the
    self-attention module grows quadratically with the increase
    of the input resolution. It results in incompatibility to the
    typical feature pyramid that is widely used in modern ob-
    ject detectors, and relatively low performance at detecting
    small objects. 

-图像金字塔使用的限制

这里解释到，因为DETR中使用的transformer，encoder的Q、K是特征图像素的展平，这使得复杂度相对于输入空间尺度是二次增长的，因此使用图像金字塔会指数级增加计算量

-整体框架

    1.使用图像金字塔
    2.encoder部分做了完全的改进，不再是transformer encoder结构，而是基于图像金字塔的融合，这个部分关注了不同尺度scale（使用SE分配不同scale权重），不同spatial（不同channel之间，使用offset聚合），不同表示representation（动态RELU）的关系，全方面动态的关注特征
    3.decoder部分也做了大量改进，首先引入了ROI pooling和box encoding部分，我感觉这个部分很精髓，box encoding替代了原来的positional encoding，作为预测框，和ROI pooling以及encoder的output feature map结合，取出对应的feature map部分内容并整合到相同的scale，concat在一起，作为encoder最终输出，其他部分和DETR原始decoder类似，创建object query作为decoder输入，经过MSA和一个FC层，得到预测参数，该参数是用于指定一个1 * 1卷积层的参数，encoder最终输出经过该1 * 1卷积层以后得到Q，再使用该Q求解后续的classification，box，query embedding



PS. 不了解encoder融合部分SE权重分配是如何进行的
PS. 其中还使用了Dynamic Relu，看公式没什么特比的，但是不了解有什么特别的用处
PS. decoder处使用了一个动态参数1*1卷积层，也许是为了契合dynamic这个思路？
PS. 这一部分如何使用动态的决定参数我没有仔细学过，以后有机会可以看看




