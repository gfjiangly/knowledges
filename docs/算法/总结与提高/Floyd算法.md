一个号称只有5行代码的算法， 由1978年图灵奖获得者、斯坦福大学计算机科学系教授罗伯特·弗洛伊德命名。该算法有于求一个带权有向图(Wighted Directed Graph)的任意两点的最短距离的算法，运用了动态规划的思想, 算法的时间复杂度为`O(V^3)`，空间复杂度`O(V^2)`。

其核心思想是，在两个顶点之间插入一个或一个以上的中转点，比较经过与不经过中转点的距离哪个更短。同时，我们需要引入2个矩阵，一个邻接矩阵D，用来计算每个相邻点的距离，也就是我们的已知条件。第二个矩阵P，则用来表示中间点k的代数。比如说P中`P[i,j]`的值就是i与j两点的中间点代数。

这个中转点的思想，我们可以想象现实中的自驾游问题，有的城市间的道路好走，比如存在高速公路，其需要的时间就越短，有的城市间只有原始的泥泞小道，行驶时就很耗时间。

比如A地直接到B地需要6小时。A地经由C地再到B地，则要1+4=5小时。A地经由C地再到D地再到B地，则要1+1+1=3小时。

![img](assets/Floyd%E7%AE%97%E6%B3%95/v2-8aa718251a4a05852caf65920de37083_720w.jpg)

进行Floyd算法的时候，也要像Dijkstra算法一样，不停的更新这两个矩阵。当我们根据一点规律变化中间点k的时候，也要遍历所有的最小距离和中间点，若`D[i,j] > D[i,k]+D[j,k]`，则矩阵D中的`D[i,j]`需要更新成`D[i,k]+D[j,k]`，矩阵P中的`P[i,j]`要改为k。在遍历掉所有点为中心之后，Floyd算法完成。

```cpp
for(int k=0; k<n; k++)  // 中转站0~k
    for(int i=0; i<n; i++)  // i为起点
         for(int j=0; j<n; j++)  // j为终点
             if(d[i][j] > d[i][k]+d[k][j])  // 松弛操作 
                 d[i][j] = d[i][k]+d[k][j]; 
```

Floyd算法求得的是多源最短路，即任意两点的最短路径；而迪杰斯特拉算法是单源最短路，即某一点到其他点的最短路径。