⭐⭐⭐⭐main()

    1 创建随机数种子相关
    2 创建整体模型



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
        
