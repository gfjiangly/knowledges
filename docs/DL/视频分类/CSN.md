- 动机**：**如何将2D的group convolution泛化成3D的group convolution。如何将3D卷积的spatial-temporal卷积和channel卷积分离？
- 做法**：**将3D卷积(c,c,3,3,3)分离为时空depthwise卷积(1,1,3,3,3)和通道卷积(c,c,1,1,1)。



