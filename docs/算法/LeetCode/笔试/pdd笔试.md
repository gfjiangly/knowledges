![image-20200901232601694](assets/pdd%E7%AC%94%E8%AF%95/image-20200901232601694.png)

测试用例：

```
输入：
    n, m = 3, 4
    nums = [
        [0, 1, 0, 0],
        [1, 0, 1, 1],
        [0, 1, 0, 0]
    ]
输出：5

输入：
    n, m = 4, 4
    nums = [
        [1, 0, 1, 1],
        [1, 1, 0, 1],
        [0, 0, 0, 0],
        [1, 1, 1, 1]
    ]
输出： 8

输入：
    n, m = 4, 5
    nums = [
        [1, 1, 0, 0, 0],
        [1, 1, 0, 0, 0],
        [0, 0, 0, 1, 1],
        [0, 0, 0, 1, 1]
    ]
输出：5
```

## 带完全标记DFS

最精妙的地方在于：把连通域的数量和标记联系在一起，不同连通域标记不同的数字，相同连通域用同一数字标记！

这样既可以求最大连通区域，也可以同时求出连通域数量（有合并）

```python
class Solution:

    def __init__(self, nums, n, m):
        self.nums = nums
        self.n = n
        self.m = m
        self.ds = [[-1, 0], [0, -1], [1, 0], [0, 1]]
        self.max_area = 0
        self.mark = 2

    def dfs(self, i, j):
        if i < 0 or i >= self.n or j < 0 or j >= self.m:
            return 0
        if self.nums[i][j] == 0 or self.nums[i][j] == self.mark:
            return 0

        self.nums[i][j] = self.mark
        area = 1
        for k in self.ds:
            area += self.dfs(i+k[0], j+k[1])
        return area

    def solution(self):
        s = 0
        for i in range(n):
            for j in range(self.m):
                if self.nums[i][j] == 0:
                    self.nums[i][j] = 1
                    area = self.dfs(i, j)
                    if area > 1:
                        self.mark += 1
                    self.max_area = max(self.max_area, area)
                    self.nums[i][j] = 0
                else:
                    s += 1
        self.max_area = min(self.max_area, s)
        print(self.max_area)
```



![image-20200901233015901](assets/pdd%E7%AC%94%E8%AF%95/image-20200901233015901.png)



## 暴力解法

```python
def solution4(nums, n, m):
    c = 0
    for i in range(1, n+1):
        for j in range(m):
            if i % nums[j] == 0:
                c += 1
                break
    print(c)
```

## 容斥原理+最小公倍数

基础知识1：如果k的公倍数可以被一个数整除，那么k一定可以被这个数整除。

基础知识2：从1到n之间的正整数中可以被正整数k整除的数的个数=n // k

也就是1k、2k、3k、...、(n//k) * k共n//k个

基础知识3：

![image-20200902113343815](assets/pdd%E7%AC%94%E8%AF%95/image-20200902113343815.png)

这里性质pi就是可以被M集合中任意一个数整除。

利用基础知识1简约M集合中元素，在这个集合中去除任意元素的公倍数，以减少集合的规模，注意这里主要目的不是为了剪枝，而是为了后面统计1到n被M中元素整除的数的数量时避免重复统计。

然后计算M集合的所有子集（不包含空集），也就是元素数为1的子集，元素数为2的子集，...，元素数为集合大小的子集。

然后利用基础知识2计算能被每个元素整除的数量，能被2个元素整除的数量，能被3个元素整除的数量，...，不能简单的把这些结果相加，因为里面有重复的结果，例如能被2整数的数有 n//2 个，能被3整除的有 n//3 个，能被2或3整除的数量不是 n//2 + n//3，而是需要减去既能被2整除的也能被3整除的（也就是被2*3=6整除的）。这个规律实际上就是容斥原理的重要组成部分。

接着利用基础知识3：容斥原理的推论，去除中间重复的，得到答案。

```python
def solution4(nums, n, m):
    # 对M进行去除公倍数，得到精简的M集合
    M = []
    for i in range(m):
        M.append(int(input()))
    M.sort()
    i = 0
    while i<=len(feature)-1:
        for j in range(i):
            if feature[i]%feature[j] == 0:
                feature.pop(i)
                i-=1
                break
        i+=1
 
    # 得到不含空集的M的所有子集
    sub_M = []
    length = len(M)
    def get(start, tem):
        sub_M.append(tem + [])
        for i in range(start, length):
            get(i+1, tem + [M[i]])
    get(0, [])
    sub_M.pop(0)
    
    # 计算公倍数
    def product(nums):
        tem = 1
        for num in nums:
            tem *= num
        return tem
 	
    # 利用容斥原理得到答案
    res = 0
    for ind in sub_M:
        g = len(ind)
        tem = product(ind)
        k = n // tem
        if g % 2 == 1:
            res += k
        else:
            res -= k
    print(res)
```

