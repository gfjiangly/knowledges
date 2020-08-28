**题目 ： 有一些正整数可以表示成若干连续质数的和（可以仅有一个）。例如，53可以表示成5+7+11+13+17和53自身，而41可以表示成2+3+5+7+11+13, 11+13+17和41自身；因此53和41表示成连续质数和的方案数分别是2和3。但是20就没有表示成连续质数的和。**

**输入一系列的n(<10000)，编程求它们的连续质数和的方案数**。

思路：记录小于当前值的质数，同时记录前缀和，加入set，计算方案值，一趟扫描。

```python
def solution(num):
    if num < 4: return num > 1
    # a = [2]  # <=n的所有质数
    s = 2
    prefix_s = {0, 2}
    res = 0
    for i in range(3, num+1):
        if i % 6 != 1 and i % 6 != 5:
            continue
        flag = True
        for j in range(5, int(math.sqrt(i))+1, 6):
            if i % j == 0 or i % (j+2) == 0:
                flag = False
                break
        if flag:
            # a.append(i)
            s += i
            prefix_s.add(s)
            if s - num in prefix_s:
                res += 1
    return res

```



关于判断是否为质数的优化：https://blog.csdn.net/afei__/article/details/80638460

质数分布的规律：大于等于5的质数一定和6的倍数相邻。例如5和7，11和13,17和19等等；但是注意，是6的倍数的邻居不一定就是质数。