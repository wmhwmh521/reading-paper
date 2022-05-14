## sparse R-CNN
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/sparse%20R-CNN/3sparse_rcnn.pdf)


粗读了一下文章整体，里面的主要思路就是改变网格式预测锚框的思路，直接预测锚框（类似yolov1的原始思路）或者说将dense改为sparse（dense和sparse指的是将多少边界框做为样本，多就是dense，少就是sparse），但是同时又不改变端到端这个思路，其中用了很多细节来实现这个想法

-整体框架

backbone也是普通的CNN，这里选用的是FPN，比较有特点的是这里有两个可学习的特征proposal boxes和prposal feature，这组参数是一一对应的，即一个proposal boxes有一个相应的prposal feature，proposal boxes作用即为锚框，之后通过卷积输入到动态头（文中与transformer中的multi-head作对比）中，prposal feature可以影响动态头中卷积核的参数，最后在经过全连接做预测和回归

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/sparse%20R-CNN/1.png)

-Learnable proposal box

这是一个固定数量的锚框集合（用以替代RPN生成的锚框），每一个锚框的参数都是（0,1）之间的值，表示归一化的边界框参数，同时这一块的参数会跟着反向传播的时候更新（不过这里我不清楚这几个参数是在模型里怎么实现的，用nn.parameters吗？）
同时文中说这些锚框的物理意义可以认为是网络学习到的目标潜在位置的数据统计，即哪些位置比较容易出现目标

-Learnable proposal feature

这个特征可以用于动态调整对应框的卷积核参数，物理意义可以理解为更加专注框中某一部分的内容

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/sparse%20R-CNN/2.png)

-Dynamic instance interactive head

ROI feat是指Learnable proposal box对应的特征图通过ROIAlign（类似于ROI pooling，在Mask R-CNN中提出）操作得到的特征图，然后是Learnable proposal feature的可以作为特征来预测conv的卷积核参数，同样这个卷积也是1 * 1卷积，类似faster R-CNN这一块的操作

文中说Learnable proposal feature的作用类似于注意力机制，通过动态的参数改变卷积核参数，从而让提取到的特征更注重不同部分的内容

同时使用了迭代结构来进一步提高性能，Learnable proposal box和Learnable proposal feature这两个参数是动态学习的，即每次新生成的候选框和特征将作为下一阶段的Learnable proposal box和Learnable proposal feature
PS.新的proposal feature是指图2中经过view后的那一部分特征，按我的理解新的proposal box应该是边界框经过回归参数的微调之后作为新的边界框，下一阶段应该指的是下一张输入图片

-Set Prediction和loss

sparse R-CNN也使用了集合预测的方式来匹配predictions和ground truth，同时它的loss也和DETR几乎是一样的，只是不同部分的loss加了相应的系数以应对样本正负例不均的问题

