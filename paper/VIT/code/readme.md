## VIT
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/VIT/5VIT.pdf)

-整体框架

先在大脑里思考一下VIT的框架流程
* 首先是输入经过分割，展平，之后做flattening的操作，后续接embedding层映射特征
* 接着参考transformer和bert的做法，加入class token以及位置编码
* 正常的MSA（多头自注意力）操作
* 接MLP做分类任务

-分割、展平、以及embedding层的实现
这三部分其实只用1个卷积层就可以实现
* 分割
  * 对于这一部分只需要让卷积核的大小等于分割窗口大小，stride = 分割窗口的size，就可以做到让卷积核每次移动都正好框到分割的内容
* 展平
  * 从NLP的角度来考虑展平是将框柱窗口内的所有像素都展平，如果原来的形状是 (batch, h, w, c), 展平后维度应该是(batch, h * w * c) 
* embedding层
  * 由于卷积层每次的操作正好是对卷积核窗口内的像素做操作，而窗口大小又等于分割好的patch，那么这样一次卷积操作的结果正好是对窗口内的所有像素做了embedding，效果等同于先展平，再接linear层，因此只要设置好卷积层的输出通道数C即可，C即为embedding后的feature维度

input:     [B, C1, H1, W1]
conv:      [B, C1, H1, W1] -> [B, C2, H2, W2]   #做卷积，相当于按窗口内做embedding
flatten:   [B, C2, H2, W2]   -> [B, C2, H2W2]   
transpose: [B, C2, H2W2] -> [B, H2W2, C2]       #将h和w的维度合并，并与C调换位置，原因是之前提到的C实际是feature，而hw是类比于NLP里的sequence的长度

PS.此处flatten操作的转换可以使用torch.flatten函数或者tensor变量自带的flatten方法，作用相同，但是起始位置不同，因为默认的start位置不同，用哪个都可以
- [torch.flatten](https://pytorch.org/docs/stable/generated/torch.flatten.html?highlight=flatten#torch.flatten)
- [FLATTEN](https://pytorch.org/docs/stable/generated/torch.nn.Flatten.html?highlight=flatten#torch.nn.Flatten)

同样transpose操作也可以使用tensor自带的方法或者torch的函数，功能是调换矩阵的不同维度位置
- [torch.transpose, transpose](https://pytorch.org/docs/stable/generated/torch.transpose.html#torch.transpose)

