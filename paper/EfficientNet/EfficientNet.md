
## EfficientNet
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/EfficientNet/7EfficientNet.pdf)

文章整体来说是探讨网络模型的深度相关参数对分类的准确率影响，给出了depth，resolution，channel这三个因素和flops计算的平衡公式，方便了模型从base，small，large等规模的缩放，同时也提出了一个自己的baseline模型

-depth，resolution，channel

对于CNN网络来说整体影响计算量的因素即为depth，resolution，channel,整体的计算量如下

W_input * H_input * C_input * W_kernel * H_kernel * C_kernel

* depth
  * 它是指网络的总体深度，具体表现在一个stage里的模块堆叠次数，如resnet50中某些stage里的CNN会重复堆叠几次
* resolution
  * 指输入他图像的分辨率
* channel
  * 指CNN层的卷积核的通道数（因为卷积核通道数决定了输出的channel）

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/EfficientNet/1.png)

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/EfficientNet/2.png)

