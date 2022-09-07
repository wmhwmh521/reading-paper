## AdaMixer: A Fast-Converging Query-Based Object Detector
***

加速收敛，本文的思路比较特殊，提取3D特征，并做一些特殊的mix，最后效果比较好

⭐ 整体框架

这篇文章我看的不是特别懂，下面的说法都是我理解的内容

文章也是基于DETR的架构，但是并没有过多的谈encoder部分的内容，而是专注decoder，在其中加入了3D feature和adaptive mixing部分

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/AdaMixer/2.png)

decoder应该是没有按照transformer的架构来做，几乎是自己重新创作了一个decoder

⭐ object query

object query包含Content vector和Positional vector，其中Positional vector经过模型调整后可以decode出Bounding Box，

⭐ 3D feature

通过对Positional vector做一些

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/AdaMixer/1.png)

⭐ adaptive mixing


