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
我按字面意思理解不太明白，于是找了篇博客看了下具体是如何实现的，如下图的4 * 4矩阵，先将矩阵分为4块，每个2 * 2，然后取出每个对应位置的像素拼接到一起，再按通道concat一起，经过一个layer norm后用线性层减少通道数，这样实现了将分辨率减少一半，通道数翻倍的操作

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/swin%20transformer/2.png)

-swin transformer block

swin transformer block是参考transformer的架构组装的，但是中间的attention部分没有使用VIT的结构，没有在整个图片上对所有像素做self attention，而是为了减少计算量，进行分窗口的自注意力，同时swin transformer block中的自注意力块有两种，分为W-MSA和SW-MSA，一般是交替使用

PS.文章这里给出了使用W-MSA结构（分窗口自注意力）和普通的MSA结构（VIT结构部分窗口）的计算复杂度，证明了分窗口确实能减少很大的计算量，具体的解释参考博客内容

-W-MSA

和VIT的MSA结构相比只是分窗口做自注意力以减少计算量，举例来说8 * 8的矩阵MSA是对64个像素整体做一次自注意力，对于W-MSA则可以分成4块，每一块16个像素，做4次16个像素的自注意力

-SW-MSA

由于W-MSA是分窗口做自注意力的，所以每个窗口只能分享窗口内的信息，而不能在窗口之间进行信息交互，为了解决这个问题，提出了可以滑动的窗口，文中给出的滑动策略是每次在横纵方向移动$\left\lfloor\frac{M}{2}\right\rfloor$个位置（M是窗口大小），举例M为4 * 4的矩阵，每次移动2格，对移动后的网格内做MSA操作，即为SW-MSA

PS.但是滑动以后窗口从4个变成了9个，增大了计算量，为了减少计算量又提出了一个trick，即每次移动不同位置的网格然后重新拼接，计算时对不同大小的框做相应大小的mask操作，这里的mask操作是通过在softmax权重处添加大量负权重实现的

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/swin%20transformer/3.png)

-Relative Position Bias

这里还有一块内容就是在做自注意力的softmax操作之前还在缩放点积处增加了一个Relative Position Bias，关于它的实现文中也没有怎么提及，但是从实验结果来看效果确实有提升，具体的实现过程也在之前提到的那篇博客上有写到，

PS.不过这里我还是有一些问题就是里面提到的relative position bias table是哪里来的呢？是根据某种规则生成的吗，还是用某种方式自学习到的

$$
\operatorname{Attention}(Q, K, V)=\operatorname{SoftMax}\left(Q K^{T} / \sqrt{d}+B\right) V
$$

