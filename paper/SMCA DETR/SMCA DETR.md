## SMCA DETR 2021 ICCV
***

-Spatially Modulated Co-Attention  = SMCA

对模型整体做了较大的改动，主要在encoder部分

-Dynamic spatial weight maps

由于DETR模型在encoder部分没有给出一些明确的训练目标或者方向，这导致了encoder部分在学习初期的盲目和收敛速度缓慢。
为了让模型的构建更加有明确意义，或者说让模型更加明确改进的方向，加入了一个权重特征图，该特征图的作用就是让encoder部分会更加偏重某一个位置，这种思路类似于预先给encoder加入了一些锚框，特征图的实现是一个高斯分布图，越靠近中心权重越大，在不同特征图做QKV时，将特征图作为偏置加在缩放点积之中，从而起到更加关注特定位置的效果

-SMCA with multi-head modulation

同样这里也使用多头策略，这个多头策略同样会影响前一步的weight map，也就是对不同的头来说会进一步对weight map做后续的微调

-SMCA with multi-scale visual features

这里也思考了多尺度的问题，文中提及了多尺度在transformer里实现的一些问题，首先就是计算复杂度
    1.如果使用多尺度来计算attention，计算量会大幅增加
    2.多尺度特征必然涉及一个融合，融合的策略很重要，直接叠加再一起或者相加过于简单

    1.对于问题1，文中提出的解决策略是提出了一个intra-scale encoding，即单独的对每个scale的feature map做attention，而不是统一resize到一个scale再一起做attention，这里设置了3个不同的scale
    2.对不同scale的feature map分别输出相应的attention系数，输入是根据object query经过FC层得来的，再进行聚合
    3.最终的聚合方式是，先考虑多头主力的单个头，在单个头中分别聚合3个scale的feature，即先对3个scale的feature单独做self attention之后的结果乘以前一步得出的attention系数，再整体相加
