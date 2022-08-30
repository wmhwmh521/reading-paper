## DN-DETR
***

论文的标题是加速DETR收敛，通过使用Query DeNoising的方式，主要看点和贡献是引入了DeNoising从而使收敛速度变快，以及一个attention mask的操作

-Set Prediction

对比DETR，它同样也使用了set prediction，但是文中指出DERT使用的匈牙利算法存在缺陷，会导致收敛速度变慢，所以文中想对这个问题进行改进从而加速收敛

-Query DeNoising

我读文章理解的这部分内容就是类似于一个数据增强的操作

对于已有的ground truth（4D形式）, 按组来做denoise的偏移，即分别对中心坐标和宽高做偏移和缩放以产生新的组，作为set prediction的新的ground truth来进行匹配，可以产生多个group

PS这里我不知道DETR的匹配到底是如何实现的，这部分是我的理解

- Attention Mask

注意面具的目的是防止信息泄露。有两种类型的潜在信息泄露。一是匹配部分可能会看到带噪声的 GT 对象并容易预测 GT 对象。另一个是 GT 对象的一个​​噪声版本可能会看到另一个版本。

因此，我们的注意掩码是确保匹配部分看不到去噪部分，而去噪组不能看到彼此，如图 4 所示。

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/DN-DETR/1.png)

-一些细节

整篇文章大体思路是构建于 DAB-DETR这篇文章，也是主要对比对象，突出的有效性主要是加速收敛

