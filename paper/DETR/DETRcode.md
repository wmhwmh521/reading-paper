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


