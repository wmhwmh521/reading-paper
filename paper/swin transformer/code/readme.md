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

x0 = x[:, 0::2, 0::2, :]  # [B, H/2, W/2, C]

x1 = x[:, 1::2, 0::2, :]  # [B, H/2, W/2, C]

x2 = x[:, 0::2, 1::2, :]  # [B, H/2, W/2, C]

x3 = x[:, 1::2, 1::2, :]  # [B, H/2, W/2, C]

x = torch.cat([x0, x1, x2, x3], -1)  # [B, H/2, W/2, 4*C]

x = x.view(B, -1, 4 * C)  # [B, H/2*W/2, 4*C]

对于前两部分具体的实现是先切片，然后cat在一起

cat和concat用法和作用都相同，作用是按某个通道将两个tensor融合在一起，要求除了被融合的通道其他shape要相同
- [TORCH.CAT](https://pytorch.org/docs/stable/generated/torch.nn.functional.pad.html?highlight=pad#torch.nn.functional.pad)

3）整形以后接一个归一化层，一个线性层减少通道数

PS.如果这里特征图的像素比不是2的整数倍则需要对其进行padding，以满足patch merging的策略，此时的padding是在右侧和下侧做padding，使用了F.pad这个函数

pad是functional下的一个方法，用来对tensor进行指定位置填充
- [TORCH.NN.FUNCTIONAL.PAD](https://pytorch.org/docs/stable/generated/torch.nn.functional.pad.html?highlight=pad#torch.nn.functional.pad)
- [说明](https://zhuanlan.zhihu.com/p/358599463)
- 举例
- 这里的p3d是pad的填充位置，其中每一对对应一个维度，从后向前
- 即(0, 1)对应的是t4d的最后一个维度（2的维度），意思是前面填充0列，后边填充1列
- (2, 2)对应的是t4d的倒数第二个维度（4的维度），意思是前面填充2列，后边填充2列
- (3, 3)对应的是t4d的倒数第三个维度（2的维度），意思是前面填充3列，后边填充3列
- 
>>> t4d = torch.empty(3, 3, 4, 2)
>>> 
>>> p3d = (0, 1, 2, 1, 3, 3) # pad by (0, 1), (2, 1), and (3, 3)
>>> 
>>> out = F.pad(t4d, p3d, "constant", 0)
>>> 
>>> print(out.size())
>>> 
torch.Size([3, 9, 7, 3])

### W-MSA 和SW-MSA
***

W-MSA的思路
- 首先对特征图进行分割，如果特征图不是窗口整数倍则padding
- 按窗口做W-MSA，此处的MSA就是普通的MSA
- 将做好W-MSA后的数据还原成分割前的样子，如果进行过padding则需去掉相应的位置

W-MSA的思路
- 如果特征图不是窗口整数倍则padding
- 首先滑动窗口再分割，调整矩阵行列位置以满足普通的W-MSA操作，同时记录相应分割区域的mask，为后续做softmax做准备
- 按窗口做W-MSA，此处的MSA就是普通的MSA
- 将做好W-MSA后的数据还原成分割前的样子
- 调整矩阵行列回原来的位置
- 去掉相应的padding



- 
