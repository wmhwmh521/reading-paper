## VIT
***

- [原论文](https://github.com/wmhwmh521/reading-paper/blob/main/paper/VIT/5VIT.pdf)


transformer架构的视觉分类器

-整体框架

整体的思路比较简单，就是将图片先经过分割，然后展平（flattened），经过embedding后作为transformer的输入，不过只用transformer的encoder部分

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/VIT/1.png)

-输入

文中图片举例的输入是将input分割成3 * 3，即为9份，然后再将每一份都展平成一个sequence，经过一个embedding以后变为D维的向量，这个embedding文中给的解释就是一个简单的矩阵乘，之后会加入position encoding，与普通transformer类似，之后输入transformer结构中

-transformer encoder

就是参考的标准的transformer结构，有些不同的是layer Norm的位置不同，标准transformer是经过多头注意力和残差相加之后做layer Norm，而这里是先做layer Norm再经过MSA（multihead self attention），只有这一个不同点，不过不太了解为什么这样改

![image](https://github.com/wmhwmh521/reading-paper/blob/main/paper/VIT/2.png)

-class token

同时文章参考了bert的多下游任务的策略，即加入了class词元，在bert中这一词元代表的整个句子级别的语义信息，而这里代表的是整个图片的语义信息，经过transformer结构提取信息后，最终的预测也是在class次元的feature上进行预测，即经过一个2层的MLP（激活函数是GELU）做分类
