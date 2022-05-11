## DETR
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/DETR/2End-to-End%20Object%20Detection%20with%20Transformers.pdf)


看了下论文的整体模型和思路，基本上就是CNN的backbone + transformer预测分类和边界框的回归，其中有一些细节，同时了解了一些概念

-Set Prediction
不同于RCNN和YOLO生成框以后用NMS去重的方式，这里使用的Set Prediction是为生成的框与真实的框做匹配，举例来说就是预测有N个框，真实图片有3个框，那么就将真实图片里的框补齐到N个，即为
3 + （N - 3）,之后再将预测的N个框和真实的N个框（包含N-3个no object标签）做两两匹配，这里还提到了匈牙利算法（查了一下是一种集合匹配的方式）
