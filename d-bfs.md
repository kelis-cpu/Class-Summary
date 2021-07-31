## 一.宽度优先搜索算法(BFS)---队列

**思想：从图中某顶点V出发，在访问了v之后依次访问v的各个未曾访问过的邻接点，然后分别从这些邻接点出发依次访问它们的邻接点，并使得“先被访问的顶点的邻接点先于后被访问的顶点的邻接点被访问，直至图中所有已被访问的顶点的邻接点都被访问到。如果此时图中尚有顶点未被访问，则需要另选一个未曾被访问过的顶点作为新的起始点，重复上述过程，直至图中所有顶点都被访问到为止。**

<u>应用：查找最短路径、求两点间最小距离</u>

```python
#个人觉得python中列表(list)比队列(queue)更好用
1.list.pop([index=-1]),默认移除列表最后一个元素并返回元素值
  list.pop(0)#出队
2.list.append(obj) #队列末尾添加新对象,入队
# 双向队列
from collections import deque
q=deque([])
```

![](D:\Typora\img\1.png)

### OJ

#### 1.二叉树的所有路径 LintCode·480

```python
def binaryTreePaths(self, root):
        # write your code here
        if root is None:
            return []
        path=""
        paths=[]
        self.find(root,path,paths)
        return paths
    def find(self,node,path,paths):
        if path=="":
            path=str(node.val)
        else:
            path=path+'->'+str(node.val)
        if not node.left and not node.right:
            paths.append(path)
            return
        if node.left:
            self.find(node.left,path,paths)
        if node.right:
            self.find(node.right,path,paths)
        return  
```

#### 2.字母大小写转换 LintCode·1032

```python
# str.isdigit() 检测字符串是否只由数字组成
# str.isalpha() 检测字符串只由字母和文字组成
# str.isalnum() 检测字符串是否只由数字和字母组成
# str.upper()/str.lower() 大小写转换
```

## 二.回溯法

**代码框架:**

```python
# 1.路径:也就是已经做出的选择
# 2.选择列表:也就是当前可以做的选择
# 3.结束条件:到达决策树底层，无法再做选择的条件
result=[]
def backtrack(路径,选择列表):
    if 满足结束条件:
        result.add()
        return
    for 选择 in 选择列表:
        做选择
        backtrack(路径,选择列表)
        撤销选择
```

[关于python对象、变量、引用的理解](https://www.cnblogs.com/qianfanwaer/p/14783204.html)

### OJ

#### 1.全排列

```python
# 标准库中的排列和组合
# import itertools
# itertools.permutations([1,2,3],1) 排列 A1/3-->[1,2,3]
# itertools.combinations([1,2,3],3) 组合 C3/3-->[1,2,3]
import copy
ans = []
def backtrack(res, n):
    global ans
    if len(res) == n:
        tmp = copy.copy(res)
        ans.append(tmp)
        return
    for i in range(1, n + 1):
        if i in res:
            continue
        res.append(i)
        backtrack(res, n)
        res.pop()
```

#### 2.N皇后问题

```python
# zip函数:返回元组列表， 函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表。

#如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用 * 号操作符，可以将元组解压为列表
#>>>a = [1,2,3]
#>>> b = [4,5,6]
#>>> c = [4,5,6,7,8]
#>>> zipped = zip(a,b)     # 打包为元组的列表
#[(1, 4), (2, 5), (3, 6)]
#>>> zip(a,c)              # 元素个数与最短的列表一致
#[(1, 4), (2, 5), (3, 6)]
#>>> zip(*zipped)          # 与 zip 相反，*zipped 可理解为解压，返回二维矩阵式
#[(1, 2, 3), (4, 5, 6)]

res = 0
N = int(input())
board = [['.' for i in range(N)] for j in range(N)]
def isValid(col, row, board):
    # 检测行列
    for i in range(N):
        if board[i][col] == 'Q':
            return False
    # 检测主对角线
    for i, j in zip(range(row - 1, -1, -1), range(col - 1, -1, -1)):
        if board[i][j] == 'Q':
            return False
    # 检测副对角线
    for i, j in zip(range(row - 1, -1, -1), range(col + 1, N)):
        if board[i][j] == 'Q':
            return False
    return True
def backtrack(row, N):
    global res
    if row == N:
        res += 1
        return
    for i in range(N):
        if not isValid(i, row, board):
            continue
        board[row][i] = 'Q'
        backtrack(row + 1, N)
        board[row][i] = '.'
```



