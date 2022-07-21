
⭐ [torch.as_tensor](https://pytorch.org/docs/stable/generated/torch.as_tensor.html?highlight=torch%20as_tensor#torch.as_tensor)

numpy转torch.tensor，可以指定dtype和device

⭐ [torch.min/max](https://pytorch.org/docs/stable/generated/torch.min.html?highlight=torch%20min#torch.min)

默认取所有元素min/max，可以指定维度

⭐ [torch.nn.functional.interpolate](https://pytorch.org/docs/stable/generated/torch.nn.functional.interpolate.html?highlight=torch%20nn%20functional%20interpolate#torch.nn.functional.interpolate)

torch的插值算法，其实也就是另一种resize

⭐ [torch.stack](https://pytorch.org/docs/stable/generated/torch.stack.html?highlight=torch%20stack#torch.stack)

按维度叠加，需要相同形状tensor

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

中的横纵坐标

⭐ 切片中None的含义 增加维度

w_ratios.shape       ->     torch.Size([3, 3]),

w_ratios[:, None].shape   ->  torch.Size([3, 1, 3]),

⭐ str(torch.tensor)

tensor也可以转字符串,可以用做dict的key
