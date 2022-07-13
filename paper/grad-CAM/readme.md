## grad-CAM
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/grad-CAM/9grad-CAM.pdf)

文章的整体思路就是如下图所示，主要是选定一个作为特征图的layer，然后通过记录正向传播和反向传播的数据，计算分类结果与该layer内容的相应梯度，得出不同的通道所对应的权重，再通过一系列图像学上的处理得到热力图

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/grad-CAM/1.png)


-推荐grad-CAM代码

https://github.com/jacobgil/pytorch-grad-cam

-一些看源码的心得

⭐ hook函数

- [register_forward_hook](https://pytorch.org/docs/stable/generated/torch.nn.Module.html?highlight=register_forward_hook#torch.nn.Module.register_forward_hook)

- [register_full_backward_hook](https://pytorch.org/docs/stable/generated/torch.nn.Module.html?highlight=register_full_backward_hook#torch.nn.Module.register_full_backward_hook)

正向传播和反向传播时的钩子函数，这两个钩子函数能够用于保存相应层的数据和梯度，为计算热力图做准备

⭐ VIT和swintransformer的应用

因为VIT和swin都将图像做了分割然后排成序列，需要将这些序列还原回完整的图像再做计算
