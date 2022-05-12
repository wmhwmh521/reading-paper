## DETR
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/DETR/2End-to-End%20Object%20Detection%20with%20Transformers.pdf)


看了下论文的整体模型和思路，基本上就是CNN的backbone + transformer预测分类和边界框的回归，其中有一些细节，同时了解了一些概念

-Set Prediction

不同于RCNN和YOLO生成框以后用NMS去重的方式，这里使用的Set Prediction是为生成的框与真实的框做匹配，举例来说就是预测有N个框，真实图片有3个框，那么就将真实图片里的框补齐到N个，即为
3 + （N - 3）,之后再将预测的N个框和真实的N个框（包含N-3个no object标签）做两两匹配，以解决样本和标签的问题，具体匹配规则是匈牙利算法（查了一下是一种集合匹配的方式）

-整体框架

CNN提feature然后transformer过一遍，再做预测分类和边界框回归，CNN和transformer都是标准的框架，可以使用resnet-50和pytorch官方实现的transformer

不同之处在于预测框和真实框的匹配规则，图中红黄框有相应匹配，绿色则是no object

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/DETR/1.png)

-一些实现细节

首先是CNN卷积后的feature map形状是d×H×W（d为channel），会变为 d×HW以符合sequence的特征，然后加上fixed的位置编码输入到encoder中

-object queries

decoder的input一部分是object queries，文中说它是可以学习的位置编码，额外加入到encoder output上作为整体的decoder input

PS.有些忘了transformer decoder的输入应该是什么，记得输入就是普通sequence

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/DETR/2.png)

-loss

loss部分相比于RCNN和YOLO，除了分类损失+回归损失，还多了一项GIoU loss，看别的论文也有蛮多加入了这一项损失的，也许有必要看看GIoU loss这篇文章
