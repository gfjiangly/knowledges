```python
class SEBlock(nn.Module):
    def __init__(self, planes):
        super(SEBlock, self).__init__()
        # SE layers
        self.fc1 = nn.Conv2d(planes, planes//16, kernel_size=1)  # Use nn.Conv2d instead of nn.Linear
        self.fc2 = nn.Conv2d(planes//16, planes, kernel_size=1)

    def forward(self, x):
        # Squeeze
        w = F.avg_pool2d(x, out.size(2))
        w = F.relu(self.fc1(w))
        w = F.sigmoid(self.fc2(w))
        # Excitation
        out = out * w
        return out
```

参考：

- https://blog.csdn.net/winycg/article/details/89285912