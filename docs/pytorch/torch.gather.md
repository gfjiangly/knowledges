torch.gather(input, dim, index, out=None) → Tensor

    Gathers values along an axis specified by dim.
    
    For a 3-D tensor the output is specified by:
    
    out[i][j][k] = input[index[i][j][k]][j][k]  # dim=0
    out[i][j][k] = input[i][index[i][j][k]][k]  # dim=1
    out[i][j][k] = input[i][j][index[i][j][k]]  # dim=2
    
    Parameters: 
    
        input (Tensor) – The source tensor
        dim (int) – The axis along which to index
        index (LongTensor) – The indices of elements to gather
        out (Tensor, optional) – Destination tensor
    
    Example:
    
    >>> t = torch.Tensor([[1,2],[3,4]])
    >>> torch.gather(t, 1, torch.LongTensor([[0,0],[1,0]]))
     1  1
     4  3
    [torch.FloatTensor of size 2x2]
index是索引，dim指定索引值在哪个维度索引，

举例：

【1,2,3;4,5,6,】，指定dim=1，也就是横向，那么索引就是列号。index的大小就是输出的大小，所以比如index是【1,0;0,0】，那么看index第一行，1列指的是2， 0列指的是1，同理，第二行为4，4 。这样就输入为【2,1;4,4】

gather在one-hot为输出的多分类问题中，可以把最大值坐标作为index传进去，然后提取到每一行的正确预测结果，这也是gather可能的一个作用。