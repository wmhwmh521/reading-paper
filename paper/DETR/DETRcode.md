⭐backbone

对resnet50做修改，其中第5个模块使用了膨胀卷积，norm_layer使用了自己重写的FrozenBatchNorm2d而不是标准的BatchNorm2d

⭐position_encoding

position_encoding的最终形状是 （b，h，w，step），根据位置由三角函数得来

我目前看来其中step应该是输入transformer的序列长度，step等于transformer的hidden_dim的一半

position_encoding与backbone抽出的feature加在一起输入transformer，也就是说position_encoding与输入到transformer的特征图形状相同

⭐backbone和backbone一起返回不同层的特征图和相应特征图的位置编码

⭐transformer

同样分encoder和decoder

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

                
                



