## AdaMixer: A Fast-Converging Query-Based Object Detector
***

加速收敛，本文的思路比较特殊，提取3D特征，并做一些特殊的mix，最后效果比较好

⭐ 3D feature

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/grad-CAM/1.png)

⭐ adaptive mixing

因为VIT和swin都将图像做了分割然后排成序列，需要将这些序列还原回完整的图像再做计算
