
## EfficientNet
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/EfficientNet/7EfficientNet.pdf)

文章整体来说是探讨网络模型的深度相关参数对分类的准确率影响，给出了depth，resolution，channel这三个因素和flops计算的平衡公式，方便了模型从base，small，large等规模的缩放，同时也提出了一个自己的baseline模型

-depth，resolution，channel

对于CNN网络来说整体影响计算量的因素即为depth，resolution，channel,不同的网络一般是单一的改变某一个因素以增加网络深度，目的是为了提高效果，但是本文提出单一提高某一因素的效果不如这三个参数整体提高，即在使用相同浮点数计算的条件下，将三者平均将会得到更好的效果

* depth
  * 它是指网络的总体深度，具体表现在一个stage里的模块堆叠次数，如resnet50中某些stage里的CNN会重复堆叠几次
* resolution
  * 指输入图像的分辨率
* channel
  * 指CNN层的卷积核的通道数（因为卷积核通道数决定了输出的channel）

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/EfficientNet/1.png)

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/EfficientNet/2.png)

公式2和公式3整体说明了文章的探索思路，在浮点数计算和内存都允许的前提下，对depth，resolution，channel这三个因素进行缩放

CNN整体的计算量如下

W_input * H_input * C_input * W_kernel * H_kernel * C_kernel

* depth
  * 对于depth，他的缩放相当于多次堆叠，因此与浮点数计算量成正比
* resolution
  * 对于输入图像分辨率，与W_input * H_input相关，因此分辨率扩大相当于2次方级别扩大，
* channel
  * 因为上一层的输出会作为下一层的输入，因此C_input * C_kernel也是2次方级别

所以公式（3）中是\alpha \cdot \beta^{2} \cdot \gamma^{2} \approx 2

