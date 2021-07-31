## 一.动态规划作用

### 1.计数

-有多少种方式走到右下角

-多少种方法选出k个数使得和是Sum

### 2.求最大值、最小值

-从左上角到右下角路径的最大数字和

-最长上升子序列

### 3.求存在性

-取石子游戏

-能否选出k个数的和为Sum

## 二.动态规划如何使用

**确定状态(两个核心:最后一步、化成子问题)--->转移方程--->开始和边界条件--->计算顺序**

### eg.

```python
# 数字三角形 http://poj.org/problem?id=1163
# 在数字三角形中寻找一条从顶部到底边的路径，使得路径上所经过的数字之和最大。路径上的每一步都只能往左下或右下走。只需求出这个最大和即可。
# 最后一步：倒数第二层到最后一层
# f[i][j]:从0层到第i层第j个数所经路径和最大值
# f[i][j]=max(f[i-1][j],f[i-1][j-1])+a[i][j],1<j<i
# f[i][j]=f[i-1][j]+a[i][j],j=0
# f[i][j]=f[i-1][j-1]+a[i][j],i=j
# 开始f[0][0]=a[0][0]
# 计算顺序：按行计算
n = int(input())
a = [[0 for i in range(n)] for j in range(n)]
f = [[0 for i in range(n)] for j in range(n)]
for i in range(n):
    tmp = input().split(' ')
    for j in range(len(tmp)):
        a[i][j] = int(tmp[j])


def solve():
    f[0][0] = a[0][0]
    for i in range(1, n):
        for j in range(i + 1):
            if j == 0:
                f[i][j] = f[i - 1][j] + a[i][j]
            elif i == j:
                f[i][j] = f[i - 1][j - 1] + a[i][j]
            else:
                f[i][j] = max(f[i - 1][j], f[i - 1][j - 1]) + a[i][j]


solve()
print(max(f[n-1]))

```

## 三.几种典型背包问题

### 1.背包型

#### a.01背包

**一共有N件物品，第i件物品的重量为w[i],价值为v[i].在总重量不超过背包承载上限W的情况下，能够装入物品的最大价值是多少？**

```python
# dp[i][j]表示将前i件物品装入限重为j的背包可以获得的最大价值,0<=i<=N,0<=j<=W
# 初始化dp[0][0,,,W]=0
# 当i>0，dp[i][j]有两种情况：
# 1.不装入第i件物品，即dp[i-1][j]
# 2.尝试装入第i件物品，即dp[i-1][j-w[i]]+v[i]
# 状态转移方程：dp[i][j]=max(dp[i-1][j],dp[i-1][j-w[i]]+v[i])
# 空间优化(滚动数组)：dp[i][j]的值只与dp[i-1][0,...,j-1]有关，所以我们可以采用动态规划常用的方法（滚动数组）对空间进行优化（即去掉dp的第一维）。需要注意的是，为了防止上一层循环的dp[0,...,j-1]被覆盖，循环的时候 j 只能逆向枚举（空间优化前没有这个限制）
# 伪代码:
dp[0,...,W] = 0
for i = 1,...,N
    for j = W,...,w[i] # 必须逆向枚举!!!
        dp[j] = max(dp[j], dp[j−w[i]]+v[i])
```

#### b.完全背包问题

**一共有N种物品，每种物品无限多个，第i个物品重量为w[i],价值为v[i].在总重量不超过背包上限W的情况下，能够装入背包的最大价值是多少？**

```python
# 目标和变量同01背包无区别，所以可以定义同01背包完全相同的状态dp
# 初始状态也相同，dp[0][0,...w]=0,表示前0种物品装入书包的最大价值为0
# 那么当i>0也有两种情况：
# 不装入第i种物品，即dp[i-1][j]
# 装入第i种物品的第n个，因为每种物品有无限个，所以此时应该转移到dp[i][j-w[i]]+v[i]
# 所以状态转移方程为dp[i][j]=max(dp[i-1][j],dp[i][j-w[i]]+v[i])
# 空间优化(滚动数组)：优化后j只能正向枚举，因为要求覆盖
# 伪代码：
dp[0,...,w]=0
for i in range(1,N+1):
    for j in range(w[i],W+1): # 必须正向枚举
        dp[j]=max(dp[i-1][j],dp[i][j-w[i]]+v[i])
```

#### c.多重背包

**一共有N种物品，第i（i从1开始）种物品的数量为n[i]，重量为w[i]，价值为v[i]。在总重量不超过背包承载上限W的情况下，能够装入背包的最大价值是多少？**

```python
# dp[i][j]=max{dp[i-1][j-k*w[i]]+v[i],0<=k<=min(j/w[i],n[i])}
# 未空间优化
for i in range(1,N+1):
    for j in range(1,W):
        for k in range(0,min(n[i],j/w[i])):
            dp[i][j]=max(dp[i][j],dp[i-1][j-k*w[i]]+k*v[i])
# 空间优化之后：
for i in range(1,N+1):
    for j in range(1,W):
        for k in range(min(n[i],j/w[i])+1,1,-1): # 只能逆向求解
            dp[j]=max(dp[j],dp[j-k*w[i]]+v[i]*k)
            
```

#### d.其他常见问题

①.恰好装满背包

初始化与普通背包问题不同，思路相同，只能dp\[0][j]=0，其它背包应该是-inf

②.求方案总数

求将背包装满或装到某一重量的方案总数

dp\[i][j]=sum(dp\[i-1][j],dp\[i][j-w[i]])

③.二维背包

二维背包问题是指限制条件包括两个(重量和体积)，此类问题与一维背包不同的是dp数组要多开

### 2.坐标型动态规划

**以坐标为状态，坐标之间的转换关系，一般利用问题给出的规则进行决策转移**

## 四.刷题

**1.最大子数组**

给定一个整数数组，找到一个具有最大和的子数组，返回其最大和。每个子数组的数字在数组中的位置应该是连续的。子数组最少包含一个数。

```python
# 最后一步：数组最后一个元素nums[n]+前n-1个数最大和
# f[i]=max(f[i-1]+nums[i],nums[i]),f[i]代表末尾为nums[i]的和最大的连续子序列
# 边界：f[0]=nums[0]
# 计算顺序：f[0],f[1],f[2]...
a = input().split(' ')
for i in range(len(a)):
    a[i] = int(a[i])
f = [0 for i in range(len(a))]
f[0] = a[0]


def solve():
    for i in range(1, len(a)):
        f[i] = max(a[i], f[i - 1] + a[i])
    return max(f)


# 优化
def solve2():
    b = a[0]
    ans = b
    for i in range(1, len(a)):
        b = max(a[i], b + a[i])
        if b > ans:
            ans = b
    return ans


print(solve())
print(solve2())
### 求最小子数组
def solve3():
    ans=b=nums[0]
    for i in range(1,len(nums)):
        b=min(b+nums[i],nums[i])
        ans=min(b,ans)
       return ans
```

**2.最小路径和**

给定一个只含非负整数的m*n网格，找到一条从左上角到右下角的可以使数字和最小的路径。

同一时间只能向下或向右移一步。

```python
# 最后一步：右下角的左一格或者上一格
# f[m-1][n-1]=min(f[m-1][n-2],f[m-2][n-1])+grid[m-1][n-1],从(0,0)到(m,n)的最小路径和
# 边界：f[0][0]=grid[0][0]
# 横、竖:f[0][j]=sum(f[0][j]),f[i][0]=sum(f[i][0])
    def solve():
    	m=len(grid)
        n=len(grid[0])
        f=[[0 for i in range(n)]for i in range(m)]
        f[0][0] = grid[0][0]
        for i in range(1, m):
            f[i][0] = f[i - 1][0] + grid[i][0]
        for j in range(1, n):
            f[0][j] = f[0][j - 1] + grid[0][j]
        for i in range(1, m):
            for j in range(1, n):
                f[i][j] = min(f[i - 1][j], f[i][j - 1]) + grid[i][j]
        return f[m - 1][n - 1]
```

**爬楼梯**

假设正在爬楼梯，需要n步才能到达顶部，但每次只能爬一步或者两步，爬到顶部的方法有多少种？

```python
n = int(input())
# 最后一步：从最后一节楼梯的前一节和前两节跳过来
# f[n-1]=f[n-2]+f[n-3],f[i]:从第一节楼梯跳到第i节楼梯有多少种方式
# 开始：f[0]=1,f[1]=1
# 计算顺序：0,1,2,3...
f = [1, 2, 0]


def solve():
    global n
    global f
    if n == 1 or n == 2:
        return 1
    for i in range(2, n):
        f[2] = f[0] + f[1]
        f[0] = f[1]
        f[1] = f[2]
    return f[2]


print(solve())

```

