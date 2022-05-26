## swin transformer
***

- [原论文]([https://github.com/wmhwmh521/reading-paper/blob/main/paper/VIT/5VIT.pdf](https://github.com/wmhwmh521/reading-paper/blob/main/paper/swin%20transformer/6swin%20transformer.pdf))

推荐一个看过的讲解博客
https://blog.csdn.net/qq_37541097/article/details/121119988


基于VIT做后续改进的CV transformer

-整体框架

swin transformer想做的是类似于resnet的框架改进，即提出类似残差块的基本结构，在图中可以看出它的基本结构由 patch merging/linear embedding层 + swin transformer block组成，经过每个块以后图像分辨率都缩小一半，通道数翻倍（类似于VGG resnet这种思路）

图像输入，经过patch partition（即和VIT一样的分割思路），输入到swin transformer block中，第一个block含有linear embedding层，剩下的block都是patch merging层，根据模型不同的型号大小后续接不同的block数量，最后再根据不同的任务接全连接层做分类或者锚框的回归

PS.但是它虽然是用了transformer的框架思路，实际实现的时候使用的都是卷积

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/swin%20transformer/1.png)

-patch partition

patch partition类似于VIT，输入同样是先被分割成几块内容，同样这里的分割也是使用卷积实现的，如分割成3 * 3即可以根据输入的分辨率计算stride和卷积核的大小，令卷积核横向移动3次纵向移动3次，即可以分别框柱相应的区域

-linear embedding

linear embedding是和patch partition使用同一个卷积层实现的，在一个卷积层里卷积核移动的时候即可以认为是patch partition部分，将卷积核的输出通道数设置为想要的embedding feature维度C，即可以认为是做了linear embedding

输入[H, W, 48]   partition后[H/4, W/4, 48]   linear embedding后[H/4, W/4, C]  这个过程是使用1个卷积实现的

PS.这里是相当于按卷积核的一小块内容来做embedding，即这一小块内容里所有通道数的像素一起生成新的embedding，得到的结果也是一个矩阵，每一个位置都是整个通道的特征一起作为embedding后的feature

-patch merging
patch merging的实现在论文中的描述只有几句话
（原文内容：第一个patch merging层链接了每组2 * 2碎片的临近特征，在4-C通道维度特征使用了一个线性层）
我按字面意思理解不太明白，于是找了篇博客看了下具体是如何实现的，大概就是

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/swin%20transformer/2.png)
