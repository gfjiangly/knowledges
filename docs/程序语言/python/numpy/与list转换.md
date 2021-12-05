注意从超过1维的数组转list不要用list()这种方式，这种方式得到的是[np.array(), ...]，里面的未被转成list，要用array内部的tolist()方法转换

![image-20191117163258543](../../../../assets/image-20191117163258543.png)

