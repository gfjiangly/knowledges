改进方向：减少震荡、自适应梯度范围

# Momentum

![image-20200713192139493](.assets/Adam/image-20200713192139493.png)

# Nesterov Accelerated Gradient

![image-20200713193717672](.assets/Adam/image-20200713193717672.png)

避免前进太快，同时提高灵敏度

# Adagrad （Adaptive gradient algorithm）

![image-20200713193922903](.assets/Adam/image-20200713193922903.png)

gt是带动量的二阶矩估计

![image-20200713195215948](.assets/Adam/image-20200713195215948.png)

**特点：**

- 前期![[公式]](https://www.zhihu.com/equation?tex=g_t)较小的时候， regularizer较大，能够放大梯度
- 后期gt较大的时候，regularizer较小，能够约束梯度
- 后期![[公式]](https://www.zhihu.com/equation?tex=g_t)较大的时候，regularizer较小，能够约束梯度
- 适合处理稀疏梯度


**缺点：**

- 由公式可以看出，仍依赖于人工设置一个全局学习率
- ![[公式]](https://www.zhihu.com/equation?tex=%5Ceta)设置过大的话，会使regularizer过于敏感，对梯度的调节太大
- 中后期，分母上梯度平方的累加将会越来越大，使![[公式]](https://www.zhihu.com/equation?tex=gradient%5Cto0)，使得训练提前结束

为什么用gt平方开根号作为约束？gt小的时候放大梯度，gt大的时候约束梯度，起到稳定梯度的作用。



矩估计：https://zhuanlan.zhihu.com/p/55780975

![image-20200713193335538](.assets/Adam/image-20200713193335538.png)

# Adadelta

![image-20200713200321847](.assets/Adam/image-20200713200321847.png)

# Adam：Adaptive Moment Estimation

![image-20200713194356208](.assets/Adam/image-20200713194356208.png)

PyTorch的betas参数，默认值也是[0.9, 0.999], eps默认值1e-8



参考：

- https://www.cnblogs.com/guoyaohua/p/8542554.html
- https://zhuanlan.zhihu.com/p/22252270