numpy.squeeze()函数

语法：numpy.squeeze(a,axis = None)

 1）a表示输入的数组；
 2）axis用于指定需要删除的维度，但是指定的维度必须为单维度（size=1），否则将会报错；
 3）axis的取值可为None 或 int 或 tuple of ints, 可选。若axis为空，则删除所有单维度的条目；
 4）返回值：数组
 5) 不会修改原数组；



增加维度用np.newaxis
如在最前面增加一个1维度，arr[np.newaxis, :]

！注意numpy没有unsqueeze函数