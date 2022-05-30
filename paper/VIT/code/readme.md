## VIT
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/VIT/5VIT.pdf)

- [PPT](https://github.com/wmhwmh521/reading-paper/blob/main/paper/VIT/Vision%20Transformer.pdf)

-整体框架

先在大脑里思考一下VIT的框架流程

*首先是输入经过分割，展平，之后做flattening的操作，后续接embedding层映射特征

*接着参考transformer和bert的做法，加入class token以及位置编码

*正常的MSA（多头自注意力）操作

*接MLP做分类任务

### 分割、展平、以及embedding层的实现
***
这三部分其实只用1个卷积层就可以实现
*分割
 *对于这一部分只需要让卷积核的大小等于分割窗口大小，stride = 分割窗口的size，就可以做到让卷积核每次移动都正好框到分割的内容
*展平
 *从NLP的角度来考虑展平是将框柱窗口内的所有像素都展平，如果原来的形状是 (batch, h, w, c), 展平后维度应该是(batch, h * w * c) 
*embedding层
 *由于卷积层每次的操作正好是对卷积核窗口内的像素做操作，而窗口大小又等于分割好的patch，那么这样一次卷积操作的结果正好是对窗口内的所有像素做了embedding，效果等同于先展平，再接linear层，因此只要设置好卷积层的输出通道数C即可，C即为embedding后的feature维度

input:     [B, C1, H1, W1]

conv:      [B, C1, H1, W1] -> [B, C2, H2, W2]   #做卷积，相当于按窗口内做embedding

flatten:   [B, C2, H2, W2]   -> [B, C2, H2W2]   

transpose: [B, C2, H2W2] -> [B, H2W2, C2]       #将h和w的维度合并，并与C调换位置，原因是之前提到的C实际是feature，而hw是类比于NLP里的sequence的长度

PS.此处flatten操作的转换可以使用torch.flatten函数或者tensor变量自带的flatten方法，作用相同，但是起始位置不同，因为默认的start位置不同，用哪个都可以
- [torch.flatten](https://pytorch.org/docs/stable/generated/torch.flatten.html?highlight=flatten#torch.flatten)
- [FLATTEN](https://pytorch.org/docs/stable/generated/torch.nn.Flatten.html?highlight=flatten#torch.nn.Flatten)

同样transpose操作也可以使用tensor自带的方法或者torch的函数，功能是做矩阵的转置，但是transpose一次只能调换2个维度的位置，所以为转置
- [torch.flatten, transpose](https://pytorch.org/docs/stable/generated/torch.transpose.html#torch.transpose)

### 位置编码以及多头注意力层的实现
***

-class token和位置编码

这两个内容的实现都是使用nn.Parameter这个方法，构建可学习的参数

input:        [B, num_pathces, C]

class token:  [B, 1, C]

position encoding:  [B, num_pathces + 1, C]

-多头注意力层

回忆多头注意力的过程，以及缩放点积的公式

qkv(): -> [batch_size, num_patches + 1, 3 * total_embed_dim]

reshape: -> [batch_size, num_patches + 1, 3, num_heads, embed_dim_per_head]

permute: -> [3, batch_size, num_heads, num_patches + 1, embed_dim_per_head]

-qkv的生成：
先使用线性层生成原来input3倍大的特征，再拆分为3组分别为qkv

input:       [B, num_pathces + 1, C]

linear:      [B, num_pathces + 1, C] -> [B, num_pathces + 1, 3C] 

linear:      [B, num_pathces + 1, C] -> [B, num_pathces + 1, 3C] 

得到3倍的qkv后使用reshape和permute改变形状

qkv(): -> [batch_size, num_patches + 1, 3 * total_embed_dim]
reshape: -> [batch_size, num_patches + 1, 3, num_heads, embed_dim_per_head]
permute: -> [3, batch_size, num_heads, num_patches + 1, embed_dim_per_head]

reshape类似于numpy的reshape，是确实改变形状然后复制一个变量出来，与view相比view不是复制变量而是对原来变量的索引
- [torch.reshape, torch.tensor.reshape](https://pytorch.org/docs/1.9.1/generated/torch.reshape.html?highlight=reshape#torch.reshape)

view返回一个改变形状的索引，改变view后的变量会改变原始变量的内容
- [torch.tensor.view](https://pytorch.org/docs/1.11/generated/torch.Tensor.view.html?highlight=view#torch.Tensor.view)

PS.reshape和view操作改变形状的思路都是先将整个矩阵按维度从后向前的维度展平成一行，然后再整形

permute是对整个tensor做维度变换的操作，可以用多个transpos实现
- [torch.permute, torch.tensor.permute](https://pytorch.org/docs/stable/generated/torch.permute.html?highlight=permute#torch.permute)



理论多头注意力：使用n个头对同一组输入做一次自注意力，相当于是需要n个额外的layer每个都对input做一次操作

实际实现：并不会创建n个头，而是只用1个头实现，只需要分组即可；

原因：假设多头注意力是N个头对input为[B, num_pathces + 1, C]的特征做多头注意力，则需要分别创建N个layer对input做一次计算，而
实际实现则一开始映射特征时就将input特征映射为[B, num_pathces + 1, N * C]，即特征会被映射为N倍，然后通过permute的方式改变形状为[B * N, num_pathces + 1, C]，
然后使用1个头对这一组数据做计算，N则可以被并入批量中,最后通过一个线性层将多个头的输出合并

-缩放点积的实现：

q乘k的转置（transpose）（此时的乘法使用@，作用类似于torch.matmul），乘缩放尺度，做softmax操作，接一个dropout层，一个linear层做映射，一个dropout层，多头注意力完毕

@: multiply -> [batch_size, num_heads, num_patches + 1, embed_dim_per_head]

transpose: -> [batch_size, num_patches + 1, num_heads, embed_dim_per_head]

reshape: -> [batch_size, num_patches + 1, total_embed_dim]

PS.这一块的操作一定是先transpos以后再reshape，才能保证是按头进行拼接，因为reshape操作的思路是先将整个矩阵按从后向前的维度展平成一行，然后再整形

-向量乘法的一些函数

三维矩阵乘法，不支持广播，只能是三维，第一维是批量，后两维是矩阵
- [torch.bmm, torch.tensor.bmm](https://pytorch.org/docs/stable/generated/torch.bmm.html?highlight=bmm#torch.bmm)

矩阵点乘操作，支持广播，等同于*（* = torch.mul）
- [torch.mul, torch.tensor.mul](https://pytorch.org/docs/1.11/generated/torch.mul.html?highlight=mul#torch.mul)

多维矩阵乘法，支持广播，等同于@（@ = torch.mul）
- [torch.matmul , torch.tensor.matmul ](https://pytorch.org/docs/1.11/generated/torch.matmul.html?highlight=matmul#torch.matmul)

-softmax
做softmax操作，沿着dim的维度上做softmax，一般用作函数
- [torch.nn.functional.softmax, torch.nn.Softmax](https://pytorch.org/docs/1.11/generated/torch.nn.functional.softmax.html?highlight=softmax#torch.nn.functional.softmax)

-dropout
按概率p做dropout，一般用做layer
- [torch.nn.Dropout，torch.nn.functional.dropout](https://pytorch.org/docs/1.11/generated/torch.nn.Dropout.html?highlight=nn%20dropout#torch.nn.Dropout)
