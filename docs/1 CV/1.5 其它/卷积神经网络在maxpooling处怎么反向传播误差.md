pooling后输入输出size不同，需要把1个输出像素的梯度传递给多个输入像素，需要保证传递的梯度总和不变，否则可能梯度爆炸。

## max pooling

max pooling的前向传播是把patch中最大的值传递给后一层，而其他像素的值直接被舍弃掉。那么反向传播也就是把梯度直接传给前一层某一个像素，而其他像素不接受梯度，也就是为0。

max pooling操作和mean pooling操作不同点在于需要记录下池化操作时到底哪个像素的值是最大，也就是max id。

![这里写图片描述](.assets/卷积神经网络在maxpooling处怎么反向传播误差/20170615211413093)

## mean pooling

mean pooling的前向传播就是把一个patch中的值求取平均来做pooling，那么反向传播的过程也就是把某个元素的梯度等分为n份分配给前一层，这样就保证池化前后的梯度（残差）之和保持不变。

![这里写图片描述](.assets/卷积神经网络在maxpooling处怎么反向传播误差/20170615205352655)
mean pooling比较容易让人理解错的地方就是会简单的认为直接把梯度复制N遍之后直接反向传播回去，但是这样会造成loss之和变为原来的N倍，网络是会产生梯度爆炸的。

参考：

- https://blog.csdn.net/qq_21190081/article/details/72871704
- https://blog.csdn.net/oBrightLamp/article/details/84635308

