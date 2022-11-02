⭐⭐⭐⭐main()

        1 创建随机数种子相关
        2 创建整体模型
        3 记录模型相关parameters，创建优化器AdamW
        4 设置动态学习率相关
        5 创建训练集train，验证集val



        target标签： (x, y, w, h)
        conv2D input： (B, C, H, W)

⭐⭐backbone

对resnet50做修改，其中第5个模块使用了膨胀卷积，norm_layer使用了自己重写的FrozenBatchNorm2d而不是标准的BatchNorm2d

⭐position_encoding

position_encoding的最终形状是 （b，h，w，step），根据位置由三角函数得来

我目前看来其中step应该是输入transformer的序列长度，step等于transformer的hidden_dim的一半

position_encoding与backbone抽出的feature加在一起输入transformer，也就是说position_encoding与输入到transformer的特征图形状相同

⭐backbone和backbone一起返回不同层的特征图out和相应特征图的位置编码pos，这其中out, pos都是list，out的内容是NestedTensor

⭐⭐transformer

同样分encoder和decoder

    __init__(self, d_model=512, nhead=8, num_encoder_layers=6,
             num_decoder_layers=6, dim_feedforward=2048, dropout=0.1,
             activation="relu", normalize_before=False,
             return_intermediate_dec=False):

    forward(self, src, mask, query_embed, pos_embed)

⭐encoder layer

encoder需要参数：参考另一篇记录不熟悉代码的md

⭐encoder

堆叠num_layers数量的encoder layer，逐级输入

⭐decoder layer

与encoder layer有所不同

输入：

    tgt, 

    memory,

    tgt_mask: Optional[Tensor],

    memory_mask: Optional[Tensor] = None,

    tgt_key_padding_mask: Optional[Tensor] = None,

    memory_key_padding_mask: Optional[Tensor] = None,

    pos: Optional[Tensor] = None,

    query_pos: Optional[Tensor] = None

其中tgt代表object query， memory代表从encoder 得到的output，其他的分别为两者所需的atten_mask和padding_mask以及positional encoding

值得注意的是tgt使用单独的positional encoding， memory使用的positional encoding和原始的src一样
                
⭐⭐DETR

⭐MLP（FFN）

几个线性层叠加，中间有激活函数，最后没有，输出作为bounding box的参数bbox_embed

⭐object query（代码中称query）

使用nn.Embedding(num_queries, hidden_dim)来实现

⭐nested_tensor

将所有input图片都扩展到相同大小并记录相应的mask

PS.transformer的encoder和decoder都会储存每次通过一个模块所得到的对应结果，所以整体返回的output都是一组output，而不是单独的最后一个模块的输出

⭐matcher

match部分首先计算整体的cost矩阵，分为三部分cost_class  cost_bbox  cost_giou，得到该矩阵后使用二分图匹配算法，即匈牙利算法得出query和target的最佳匹配方式

        cost_class = -out_prob[:, tgt_ids]

        out_prob shape = [batch_size * num_queries, num_classes]
        tgt_ids  shape = [batch_size * num_target_boxes]

这里使用tensor做切片得到的结果是，第一个维度全取，第二个维度开始按照tgt_ids的内容按顺序取相应的维度内容

        a
        tensor([[ 2.1696, -0.1447, -0.5050, -0.0133],
                [ 0.2243, -1.4616, -0.4507, -0.3227],
                [ 0.4810, -0.4079,  0.5577,  0.9265],
                [ 1.6416,  1.3855,  2.3216, -0.3384]])

        b = torch.tensor([2, 1])

        a[:, b]
        tensor([[-0.5050, -0.1447],
                [-0.4507, -1.4616],
                [ 0.5577, -0.4079],
                [ 2.3216,  1.3855]])
                
⭐cost_class  cost_bbox  cost_giou

cost_class为分类损失 cost_class = 1 - proba[target class] 其中的1为常数被省略，这里我理解的loss物理意义就是分类错误的概率，意思是本来该是分为这一类却没有分到这一类的概率

cost_bbox为锚框损失，是预测锚框out_bbox和GT标签tgt_bbox之间的1-范数

PS.这里我不清楚为什么使用1-范数

cost_giou为giou loss， 其中box_cxcywh_to_xyxy的作用为将box从（x， y， w， h）转换成（x1， y1， x2， y2），其中x和y为center中心坐标，转换后为box左上角坐标和右下角坐标

        cost_giou = -generalized_box_iou(box_cxcywh_to_xyxy(out_bbox), box_cxcywh_to_xyxy(tgt_bbox))

在求锚框和GT的IOU时，使用了广播机制

        lt = torch.max(boxes1[:, None, :2], boxes2[:, :2])
        
        boxes1.shape : [anchors_num, 4]
        boxes2.shape : [classes_num, 4]
        boxes1[:, None, :2].shape : [anchors_num, 1, 2]
        torch.max(boxes1[:, None, :2], boxes2[:, :2]).shape 
        = [anchors_num, classes_num, 2]

https://blog.csdn.net/weixin_43981194/article/details/124868957

⭐Giou

https://zhuanlan.zhihu.com/p/359982543

计算Giou的损失，loss = 1- Giou，同理这里1被省略了

        cost_giou = -generalized_box_iou(box_cxcywh_to_xyxy(out_bbox), box_cxcywh_to_xyxy(tgt_bbox))
        
⭐匈牙利算法

求解二分图的最佳匹配方式

        from scipy.optimize import linear_sum_assignment
        linear_sum_assignment()
        
        output: row_indexlist, column_indexlist

⭐⭐数据集相关

⭐数据集transform

按顺序，分别做50%概率水平翻转，

    if image_set == 'train':
        return T.Compose([
            T.RandomHorizontalFlip(),
            T.RandomSelect(
                T.RandomResize(scales, max_size=1333),
                T.Compose([
                    T.RandomResize([400, 500, 600]),
                    T.RandomSizeCrop(384, 600),
                    T.RandomResize(scales, max_size=1333),
                ])
            ),
            normalize,
        ])

⭐T.RandomResize

该resize函数会从给定的scales序列中随机挑选出一个scale用来缩放，具体做法就是将w和h较小的那一个缩放到scale的大小，同时还要保证缩放后大的一个边不会超过max_size,同时也会对tartget label进行相应的缩放

⭐T.RandomSizeCrop(384, 600)

        同时处理image和target的crop裁剪操作，可以设定crop后的部分长度最大值与最小值

        使用torchvision.transforms.RandomCrop.get_params()方法获取在image中随机的裁剪位置，返回(top，left，h，w)，是裁剪后的image相对原图的位置

        crop(image, target, region)
        target：(x1, y1, x2, y2)
        region: (top，left，h，w)


        根据原图和RandomCrop.get_params()方法返回的(top，left，h，w)对target做相应的裁剪
        具体思路：
        1.在经过crop操作以后，image从原始的左上角为坐标中心转移到了以crop部分左上角为中心，即从(0, 0)转移到(left, top) 
        2.此时原来的target坐标仍然以(0，0)为中心，用target坐标减去(left，top)即可将它转换为以(left，top)为中心的坐标
        cropped_boxes = boxes - torch.as_tensor([j, i, j, i]) 
        3.转换后的坐标会与region的[w, h]进行比较，如果大于[w, h]，则说明target的边框已经在region之外，此时需要将target限制在region之内
        max_size = torch.as_tensor([w, h], dtype=torch.float32)
        cropped_boxes = torch.min(cropped_boxes.reshape(-1, 2, 2), max_size)
        4.将转换坐标后的cropped_boxes如果有小于0的坐标，说明该坐标不在region的区域内，因此只能取它在region部分内的box
        cropped_boxes = cropped_boxes.clamp(min=0)
        5.计算crop操作之后的area的面积
        area = (cropped_boxes[:, 1, :] - cropped_boxes[:, 0, :]).prod(dim=1)

⭐⭐⭐创建一个标准的torch数据集过程

        1.确定dataset的目录
        2.确定dataset的预处理transform方式
        3.如果有pytorch标准库，可以从标准库里继承一个dataset类，否则可以自己写，该类的功能是逐个返回data和对应的label
        此处使用的是继承了torchvision.datasets.CocoDetection类，在此基础上重写

⭐⭐模型返回参数相关

模型会返回3个参数，分别为model本身，criterion，postprocessors

        return model, criterion, postprocessors
        model                   模型本身
        criterion               计算loss相关
        postprocessors          将模型output转化为coco格式

criterion的实例类

        class SetCriterion(nn.Module):
                """ This class computes the loss for DETR.
                The process happens in two steps:
                        1) we compute hungarian assignment between ground truth boxes and the outputs of the model
                        2) we supervise each pair of matched ground-truth / prediction (supervise class and box)
                """

        
