匹配多种格式图片

```python
import os
import glob

pattern = os.path.join('../data/images', '*.[png,jpg,jpeg,bmp]')
img_list = glob.glob(pattern)
```

