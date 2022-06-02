
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


$$
\begin{array}{ll}
\max _{d, w, r} & \operatorname{Accuracy}(\mathcal{N}(d, w, r)) \\
\text { s.t. } & \mathcal{N}(d, w, r)=\bigodot_{i=1 \ldots s} \hat{\mathcal{F}}_{i}^{d \cdot \hat{L}_{i}}\left(X_{\left\langle r \cdot \hat{H}_{i}, r \cdot \hat{W}_{i}, w \cdot \hat{C}_{i}\right\rangle}\right) \\
& \operatorname{Memory}(\mathcal{N}) \leq \text { target_memory } \\
& \operatorname{FLOPS}(\mathcal{N}) \leq \text { target_flops }
\end{array}
$$

-整体框架

CNN提feature然后transformer过一遍，再做预测分类和边界框回归，CNN和transformer都是标准的框架，可以使用resnet-50和pytorch官方实现的transformer

不同之处在于预测框和真实框的匹配规则，图中红黄框有相应匹配，绿色则是no object

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/DETR/1.png)

-一些实现细节

首先是CNN卷积后的feature map形状是d×H×W（d为channel），会变为 d×HW以符合sequence的特征，然后加上fixed的位置编码输入到encoder中

-object queries

decoder的input一部分是object queries，文中说它是可以学习的位置编码，额外加入到encoder output上作为整体的decoder input，有一些解释说这个位置编码可以更倾向于图像的不同位置，这样可以生成对应不同位置的边界框

PS.有些忘了transformer decoder的输入应该是什么，记得输入就是普通sequence

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/DETR/2.png)

-loss

loss部分相比于RCNN和YOLO，除了分类损失+回归损失，还多了一项GIoU loss，看别的论文也有蛮多加入了这一项损失的，也许有必要看看GIoU loss这篇文章
