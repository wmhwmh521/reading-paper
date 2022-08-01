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

对tensor指定维度进行按int或者list切片

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
