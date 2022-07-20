⭐ resize

根据设定的min_size和max_size，将输入图片缩放到这两者之一，取决于缩放比例哪一个更大，缩放时使用双线性插值算法

默认是与min_size对齐缩放，不满足时再与max_size对齐

resize同时要对target目标信息进行缩放

⭐ normalization

将输入图片按照预设好的mean和std做标准化处理

⭐ batch_images

将图片打包成最终输入，为了平衡不同大小的图片，先求出该batch中最大的size，然后向上调整到size_divisible的倍数作为该batch的image最终大小

将所有图像都放到左上角对齐

⭐⭐ forward函数

normalization → resize 并记录resize后形状 → batch_images → 返回batch_images，resize后形状，target信息
