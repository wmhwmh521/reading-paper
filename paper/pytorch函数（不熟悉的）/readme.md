⭐ [torch.view/reshape]

view和reshape功能是一样的，先展平所有元素在按照给定shape排列，view函数只能用于内存中连续存储的tensor，permute等操作会使tensor在内存中变得不再连续，此时就不能再调用view函数，reshape则不需要依赖目标tensor是否在内存中是连续的

⭐ [torch.as_tensor](https://pytorch.org/docs/stable/generated/torch.as_tensor.html?highlight=torch%20as_tensor#torch.as_tensor)

numpy转torch.tensor，可以指定dtype和device

⭐ [torch.min/max](https://pytorch.org/docs/stable/generated/torch.min.html?highlight=torch%20min#torch.min)

默认取所有元素min/max，可以指定维度

⭐ [torch.nn.functional.interpolate](https://pytorch.org/docs/stable/generated/torch.nn.functional.interpolate.html?highlight=torch%20nn%20functional%20interpolate#torch.nn.functional.interpolate)

torch的插值算法，其实也就是另一种resize

⭐ [torch.stack](https://pytorch.org/docs/stable/generated/torch.stack.html?highlight=torch%20stack#torch.stack)

生成一个新的维度，在该维度叠加，需要相同形状tensor

⭐ [TORCH.CAT](https://pytorch.org/docs/stable/generated/torch.cat.html?highlight=torch%20cat#torch.cat)

叠加tensor，与stack类似，但不生成新的维度

⭐ [TORCH.UNSQUEEZE](https://pytorch.org/docs/stable/generated/torch.unsqueeze.html#torch.unsqueeze)

在tensor指定维度插入1个dimension

⭐ [TORCH.TENSOR.NEW_FULL](https://pytorch.org/docs/stable/generated/torch.Tensor.new_full.html?highlight=new_full#torch.Tensor.new_full)

返回一个与tensor的dtype和device相同的tensor，可以指定形状和value

⭐ [TORCH.TENSOR.COPY_](https://pytorch.org/docs/stable/generated/torch.Tensor.copy_.html?highlight=copy_#torch.Tensor.copy_)

复制tensor的内容，复制src的内容到tensor

⭐ [TORCH.JIT.ANNOTATE](https://pytorch.org/docs/stable/generated/torch.jit.annotate.html?highlight=torch%20jit%20annotate#torch.jit.annotate)

变量声明操作

⭐ [TORCH.MESHGRID](https://pytorch.org/docs/stable/generated/torch.meshgrid.html?highlight=torch%20meshgrid#torch.meshgrid)

分别传入行坐标和列坐标，生成网格行坐标矩阵和网格列坐标矩阵

例如
>>> x = torch.tensor([1, 2, 3])
>>> 
>>> y = torch.tensor([4, 5, 6])
>>> 
>>> grid_x, grid_y = torch.meshgrid(x, y, indexing='ij')
>>> 
>>> grid_x
>>> 
tensor([[1, 1, 1],
        [2, 2, 2],
        [3, 3, 3]])
>>> grid_y
>>> 
tensor([[4, 5, 6],
        [4, 5, 6],
        [4, 5, 6]])
        
其实是分别记录了

[[1, 4], [1, 5], [1, 6],

[2, 4], [2, 5], [2, 6],

[3, 4], [3, 5], [3, 6]]

中list[1, 4]的1和4，以此类推

⭐ 切片中None的含义 增加维度

w_ratios.shape       ->     torch.Size([3, 3]),

w_ratios[:, None].shape   ->  torch.Size([3, 1, 3]),

⭐ str(torch.tensor)

tensor也可以转字符串,可以用做dict的key

⭐ [TORCH.CLAMP](https://pytorch.org/docs/stable/generated/torch.clamp.html#torch-clamp)

限定并改变输入tensor的最大最小值

⭐ [TORCH.SPLIT](https://pytorch.org/docs/stable/generated/torch.split.html#torch.split)

对tensor指定维度进行按int或者list切片，返回切片后包含所有tensor的tuple

⭐ [TORCH.TOPK](https://pytorch.org/docs/stable/generated/torch.topk.html#torch.topk)

对tensor指定维度排序，并取top k个值，且返回索引

⭐ [TORCH.GE](https://pytorch.org/docs/stable/generated/torch.ge.html?highlight=torch%20ge#torch.ge)

按元素计算A > B, 返回包含 True和False的矩阵  等同于用>

⭐ [TORCH.LOGICAL_AND](https://pytorch.org/docs/stable/generated/torch.logical_and.html?highlight=torch%20logical_and#torch.logical_and)

按元素做逻辑与，返回包含 True和False的矩阵    等同于用&

⭐ [TORCH.WHERE](https://pytorch.org/docs/stable/generated/torch.where.html?highlight=torch%20where#torch.where)

torch.where(condition)

如果只有condition一个arg，则返回condition中不为零的索引

torch.where(x > 0, x, y)， 按元素对condition (x > 0)计算，满足condition返回x，不满足返回y

⭐ [TORCH.NONZERO](https://pytorch.org/docs/stable/generated/torch.nonzero.html#torch.nonzero)

返回tensor非零索引

⭐ [TORCH.SQUEEZE](https://pytorch.org/docs/stable/generated/torch.squeeze.html?highlight=squeeze#torch.squeeze)

去除tensor中所有size为1的dimension，也可以去除给定位置dim的size = 1

⭐ [TORCH.FLATTEN](https://pytorch.org/docs/stable/generated/torch.flatten.html?highlight=flatten#torch.flatten)

对tensor按设置的起始和结束dim进行展平

⭐ [TORCH.SPLIT](https://pytorch.org/docs/stable/generated/torch.split.html#torch.split)

在指定dim对tensor做切片，切片大小可以相同也可以不同，可以设置

⭐ [TORCH.TOPK](https://pytorch.org/docs/stable/generated/torch.topk.html#torch.topk)

在指定dim取top K个值最大的value，并返回相应的index

⭐ [TORCH.nn.MODULELIST](https://pytorch.org/docs/stable/generated/torch.nn.ModuleList.html?highlight=modulelist#torch.nn.ModuleList)

返回一个类似python list的类型，可以像使用list一样使用它但是只包含torch里的MODULE

⭐ [TORCH.CUMSUM](https://pytorch.org/docs/stable/generated/torch.cumsum.html#torch.cumsum)

返回某一维度的持续累加值，按index顺序累加，具体参考文档

⭐ [copy.deepcopy]

返回完全某一变量完全的复制，不会出现指引用地址的情况

⭐⭐ [MultiheadAttention](https://pytorch.org/docs/stable/generated/torch.nn.MultiheadAttention.html?highlight=multiheadattention#torch.nn.MultiheadAttention)

pytorch官方的多头注意力模块，创建模型时主要需要embed_dim, num_heads, dropout这三个参数

embed_dim指输入和输出的dim， num_heads指多头注意力头的个数，dropout指丢弃法参数

正向传播forward时，需要输入qkv三者，同时可以加入attn_mask和key_padding_mask

⭐q形状为(L, N, E_q)，L为target sequence的length，N为batch size，E_q为embedding dimension，这里query的L决定了输出的长度每个query对应一个输出

https://zh-v2.d2l.ai/chapter_attention-mechanisms/attention-scoring-functions.html

⭐k形状为(S, N, E_k)，S为source sequence的length，N为batch size，E_k为embedding dimension 

⭐v形状为(S, N, E_v)，S为source sequence的length，N为batch size，E_v为embedding dimension 

⭐attn_mask只用于Decoder训练时的解码过程，作用是掩盖掉当前时刻的信息，让模型只能看到除当前时刻（包括）之外的信息

⭐key_padding_mask指的是在encoder和Decoder的输入中，由于每个batch的序列长短不一，被padding的内容需要用key_padding_mask来标识出来，然后在计算注意力权重的时候忽略掉这部分信息

https://blog.csdn.net/weixin_41811314/article/details/106804906

⭐output：attn_output 形状为(L, N, E) 其中E = embed_dim

⭐ [TORCH.CDIST](https://pytorch.org/docs/stable/generated/torch.cdist.html?highlight=cdist#torch.cdist)

计算两个批量tensor之间的n范数，需要x1和x2批量B相同同时一个维度M相同，计算式先用x1的P dimension上第一个元素与x2的R dimension上所有元素都计算一次，再用x1的P dimension上第二个元素与x2所有计算，以此类推

        x1 (Tensor) – input tensor of shape B×P×M.
        x2 (Tensor) – input tensor of shape B×R×M.
        output (Tensor) – output tensor of shape B×P×R.
        
⭐ [TORCH.UNBIND](https://pytorch.org/docs/stable/generated/torch.unbind.html#torch.unbind)       

去除tensor某个维度并返回一个切片的tuple

⭐ [TORCH.CLAMP](https://pytorch.org/docs/stable/generated/torch.clamp.html?highlight=clamp#torch.clamp)       

将tensor内的值限定在某个范围[min, max]之内，超出范围的值会被设置为min和max

⭐ [TORCH.ALL](https://pytorch.org/docs/stable/generated/torch.all.html#torch.all)       

检查tensor所有的值中是否有不为True的，如果有返回false，全为True则返回True

⭐ [register_buffer](https://pytorch.org/docs/stable/generated/torch.nn.Module.html?highlight=register_buffer#torch.nn.Module.register_buffer)       

为module添加一个参数，但是不应该被视为是模型的一个参数。举例来说就是BN层的running_mean是根据批量里的数据得到的，不应该是模型的参数，但是也是模型状态的一部分。
