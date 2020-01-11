Q1: 中心点特征图维度是[batch, num_class, height, width]还是[batch, num_class, width, height]？与什么因素有关？

A1: 这取决于输入的images张量的维度排布。先高后宽与OpenCV保持一致。

