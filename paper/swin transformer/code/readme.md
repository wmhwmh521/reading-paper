## swin-Transformer
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/swin%20transformer/6swin%20transformer.pdf)

- [PPT](https://github.com/wmhwmh521/reading-paper/blob/main/paper/swin%20transformer/Swin-transformer.pdf)

-整体框架

先在大脑里思考一下swin-Transformer的框架流程

* 和VIT一样，先做patch partition，然后做embedding
* 后续接不同大小的stage，每个stage包含一个patch merging层和一个MSA模块，这个MSA模块以transformer架构为基准，但是中间改变了MSA的结构，使用了W-MSA和SW-MSA，同时W-MSA和SW-MSA是链接使用的
* W-MSA是在小区域内做MSA，不是整个图像，这样可以减少计算量
* SW-MSA是同样在小区域内做MSA，但是window的位置有所偏移，这样可以让不同的区域共享信息，同时这里为了减少计算还做了一些额外的移动像素操作，特定的trick
* 没有使用position encoding，而是使用了自己的Relative position bias
* 后续接正常的MLP做分类或者其他预测任务


### patch partition and embedding
***
这部分的实现和VIT一样，只需1个卷积层，不再赘述


### patch merging
***

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/swin%20transformer/2.png)

patch merging的实现之前已经提过，具体的实现过程在源码里是这样做的：
1）按位置切片
2）对不同通道concat在一起
3）整形以后接一个线性层
