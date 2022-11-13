## DAB-DETR
***

这篇文章针对object query的查询提出了一些见解和想法，指出DETR收敛速度的问题主要是因为cross attention部分，具体来说是decoder这部分的query，无法很好的在encoder输出的feature map上找到目标位置，同时在decoder更新query参数时，它的具体意义也并不明确，这些问题导致了收敛速度缓慢，文章主要针对这些问题做改进，在decoder部分做了改动，没有改变encoder部分。

-LEARNING ANCHOR BOXES DIRECTLY

文中提出了直接将anchor box引入到decoder中，而不是像原始DETR一样在最后才用线性层输出anchor box，原因是在引入anchor以后，可以根据anchor的锚点让decoder更加明确目前的注意力位置，同时还可以在decoder模块的更新中对anchor进行实时更新，使decoder所做的工作更具有可解释性。

具体做法是引入anchor box，利用它来生成decoder部分的positional encoding，并在decoder的self attention和cross attention部分中以不同的形式加入进去

-decoder Self-Attn

在这部分，将positional encoding全部改为了使用anchor box生成的PE，再加入到self atten中的Q和K中进行计算
    #Cq，decoder输入的embedding
    #Aq，anchor box
    Pq = MLP(PE(Aq))
    PE(Aq) = PE(xq, yq, wq, hq) = Cat(PE(xq), PE(yq), PE(wq), PE(hq)).
    Qq = Cq + Pq, Kq = Cq + Pq, Vq = Cq,
具体过程如上，是将anchor的4个参数分开计算再叠加到一起，最后通过MLP改变维度
    PE: R → RD/2，即PE操作会降维一半，之后cat一起在MLP: R2D → RD
PS.这里我不清楚为什么是这样，PE: R → RD/2，即PE操作会降维一半，之后cat一起在MLP: R2D → RD，为什么不直接cat一起再降维呢？

-decoder Cross-Attn

    Qq = Cat(Cq, PE(xq, yq) · MLP(csq)(Cq)),
    Kx,y = Cat(Fx,y, PE(x, y)), Vx,y = Fx,y,

Cross-Attn部分不同之处在于更改了Q和K的具体组成部分，其中K的positional encoding加入了只包含anchor box（x，y）的PE，这里按照我对论文的理解是x，y参数代表了当前query比较关注的位置，通过关注位置引入positional encoding能更加专注局部，同时也有有利于后续anchor更新时query的更新

Q部分类似于K，但是在PE处还多加了一个MLP(csq)(Cq)操作，文中给出的解释是这部分cross atten为了综合考虑content和positional两部分，将Cq和PE加在一起，这里我的理解是，feature map和anchor的scale不相同，直接套用anchor的PE可能会出现问题，因此根据Cq content做一个rescale的系数与PE做点积

-ANCHOR UPDATE

decoder每个模块的output都会输出∆x, ∆y, ∆w, ∆h，作为对anchor的微调，微调后的anchor作为下个decoder模块的输入，这样使得每个decoder的工作变得更有可解释性

-WIDTH & HEIGHT-MODULATED GAUSSIAN KERNEL

在具体做cross attention部分，没有使用原始的缩放点积，而是在缩放点积的基础上，还加入了一个缩放系数scale，

    Attn((x, y), (xref, yref)) = (PE(x) · PE(xref) + PE(y) · PE(yref))/√D
    ModulateAttn((x, y), (xref, yref)) = (PE(x) · PE(xref)wq,ref
    wq + PE(y) · PE(yref)hq,ref hq )/√D,
    wq,ref, hq,ref = σ(MLP(Cq))

这里文中的解释是通过对X和y分别做不同尺度的缩放，可以更好匹配不同尺度的特征图对象

PS.我感觉是不知道这个操作有多有用，后面还有个改变positional encoding参数的操作，感觉差不多

PS.整体看下来这篇文章主要思路还是对decoder的改进，再就是引入了anchor box直接参与运算的思路

PS.自己做的话可以尝试对transformer结构做改进，decoder部分应该确定用anchor box + ROI pooling那种思路来做

