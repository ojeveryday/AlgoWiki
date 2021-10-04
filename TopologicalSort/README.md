# 拓扑排序算法

作者：wu2meng3

拓扑排序是一个有向无环图（ directed acyclic graph ，简称 DAG ）所有结点的线性序列，满足以下两个条件：

1. 每个结点只出现一次。
2. 若存在一条从结点 A 指向结点 B 的路径，则在序列当中 A 出现在 B 之前。

若一幅图存在环路，则无法进行拓扑排序。另外，对于一幅图，其拓扑排序结果并不唯一。我们采用《算法导论》第22章的规范来定义图：$G=(V,E)$，其中 $V$ 是代表结点的集合，$E$代表边的集合，$|V|$ 和 $|E|$ 分别代表结点和边的个数。$(i,j) \in E$ 代表存在一个从结点 $i$ 指向结点 $j$ 的有向边。因为我们本章中主要研究稀疏图 ( sparse graph )，所以我们采用邻接表（ adjacency list ）的方式来表示图，其空间开销是 $O(|V|+|E|)$。

例子：如下图所示，我们定义了一幅有向无环图，

```python
class DirectedGraphNode:
  
    def __init__(self, x):
        self.label = x
        self.neighbors = []

graph = [DirectedGraphNode(i) for i in range(0, 6)]
graph[0].neighbors = [graph[1], graph[2], graph[3]]
graph[1].neighbors = [graph[4]]
graph[2].neighbors = [graph[4], graph[5]]
graph[3].neighbors = [graph[4], graph[5]]
```

其拓扑排序可以是: `0->1->2->3->4->5`，也可以是 `0->3->2->5->1->4`。

![BFS](/Users/billywu/Coding/AlgoWiki/AlgoWiki/TopologicalSort/BFS.png)

![DFS](/Users/billywu/Coding/AlgoWiki/AlgoWiki/TopologicalSort/DFS.png)

## 拓扑排序的应用

举个穿衣服的例子，穿鞋子之前要穿袜子，以及先穿衬衫再穿外套。穿袜子和穿鞋子这两个任务之间就存在完成次序的依赖，而穿鞋子和穿衬衫并没有这种依赖。通过拓扑排序算法，我们就可以制定一个按次序完成任务的计划，比如说按照袜子，鞋子，衬衫，外套这样的次序来着装。

另外，采用 `Makefile` 进行编译的过程中也要用到拓扑排序对于文件和库的依赖关系进行梳理。

## 拓扑排序的两个模板

使用以下两个模板，我们可以判断一幅图是否可以进行拓扑排序，如果无法进行拓扑排序，输出空列表，如果可以进行拓扑排序则输出一种可能的拓扑排序之后的序列。

### 宽度优先搜索 （ Breadth-first search， BFS ）：

#### 思路

对于 BFS ，我们需要介绍**入度**（ indegree ）的概念。在有向图当中，一个结点的入度表示有多少条边指向它。对应的还有一个概念叫**出度** （ outdegree ），代表从一个结点出发有多少条边，但是在拓扑排序当中我们只会用到入度。入度为 0 的点的集合将作为 BFS 的起点。如果没有入度为零的点，则无法进行拓扑排序。BFS 由先进先出 （First In, First Out, FIFO）的队列 （ queue ）数据结构来实现。BFS 算法实现拓扑排序的文字描述如下：

1. 遍历邻接表中所有结点和边，计算每个结点的入度。
2. 从入度为 0 的结点出发进行 BFS ，将入度为 0 的结点放入输出列表。遍历入度为 0 结点的邻接结点时将这些邻接结点的入度减 1，如果其入度是 0 ，则放入队列。

**时间复杂度**：计算入度的循环需要遍历每个结点和每条边，BFS 循环也要遍历每一条边并且将每个结点入列和出列一次，入列和出列操作都是 $O(1)$, 所以总的时间复杂度是 $O(|V|+|E|)$ 。

**空间复杂度**：我们需要一个数组或者字典来存储每个结点的入度，还需要一个队列来存储入度为 0 的结点，总的空间复杂度为 $O(|V|)$ 。

#### 代码

```python
import collections

class Solution(object):

	def topSort(self, graph: Dict[int, List[int]]) -> List[int]:
		"""
		@type: 邻接表表示的有向图 graph
		@rtype: 图的结点列表 topoOrder
		"""
		# 计算入度
        inDegree = self.getInDegree(graph)
				
        # 将入度为 0 的结点放入队列 queue 和哈希集合 visited 防止重复遍历
        startNodeList = [ node for node in graph if inDegree[node] == 0]
        # 用 deque 实现队列
        queue = collections.deque(startNodeList)
        visited = set(startNodeList)
        # 保存结果的数组
        topoOrder = []
        # 判断是否存在环
        count = len(graph)

        # 广度优先搜索
        while queue:
            node = queue.popleft()
            topoOrder.append(node)

            for neighbor in node.neighbors:
                if neighbor in visited:
                    continue
                inDegree[neighbor] -= 1
                if inDegree[neighbor] == 0:
                    queue.append(neighbor)
                    visited.add(neighbor)

        if count != 0:
            return []
        return topoOrder
```

### 深度优先搜索（ Depth-first search， DFS）

#### 思路

一句话总结就是**拓扑排序就是图后序遍历的倒序输出**。我们现在将这句话展开来理解。根据《算法导论》第 22 章，我们可以在 DFS 的过程中给每个结点盖两个时间戳，分别是发现这个结点的时间 $d[u]$和遍历完该结点所有邻接结点的时间 $f[u]$，其中 $u \in V$ 。对于一个结点 $u$，DFS 算法遍历完其所有子结点之后才会判断其遍历结束，更新时间戳 $f[u]$。可以证明如果图中存在一条从 $u$ 指向 $v$ 的边，那么 $f[v] < f[u]$ 。所以，按照 $f[u]$ 的逆序输出结点就可以得到拓扑排序之后的序列。该算法有点类似回溯算法，我们需要在递归之前和递归之后更新每个结点的状态。每个结点有三种的可能的状态，分别是：
* `UNVISITED`：未被发现；
* `VISITING`：已发现，但是邻接结点还未遍历完；
* `VISITED`：已发现，邻接结点已经遍历完。

所有结点的初始状态都是`UNVISITED`，在遍历某结点的所以邻接结点进入下一层递归之前要将该结点的状态设为 `VISITING`，所有邻接结点都遍历完递归返回之后将其状态更新为 `VISITED`。

**时间复杂度**：在 DFS 过程中，每一个结点和每一条边都会被访问一次，将一个结点按照完成时间 $f[u]$的顺序放入输出数组需要 $O(1)$ 的时间，所以总的时间复杂度是 $O(|V|+|E|)$。

**空间复杂度**：我们需要 `states` 数组或者字典来保存每个结点的状态，所以空间复杂度是 $O(|V|)$ 。

```python
from enum import Enum
# 遍历过程中结点的三种状态
class State(Enum):
    # 《算法导论》中的 WHITE 状态：未被发现
    UNVISITED = 0
    # 《算法导论》中的 GREY 状态：已发现，但是邻接结点还未遍历完
    VISITING = 1
    # 《算法导论》中的 BLACK 状态：已发现，邻接结点已经遍历完
    VISITED = 2

class Solution:

    def topSort(self, graph: List[DirectedGraphNode]) -> List[int]:
        numNodes = len(graph)
        states = {node: State.UNVISITED for node in graph}
        res = []

        for i in range(numNodes):
            # 如果存在环，返回空列表
            if (self.hasCycle(graph, graph[i], states, res)):
                return []
        
        # 倒序输出后序遍历结果
        return res[::-1]
		
    # 判断是否存在环，存在则返回 True
    def hasCycle(self, graph, node, states, res):
        # 如果该结点被发现两次，说明有环
        if states[node] == State.VISITING:
            return True

        # 如果该结点已经遍历结束，则该递归分支结束
        if states[node] == State.VISITED:
            return False
        
        # VISITING 代表该结点已经被发现，但是其邻接结点还未遍历完
        states[node] = State.VISITING
        
        # 递归处理子结点，将子结点加入 res 列表
        for child in node.neighbors:
            if (self.hasCycle(graph, child, states, res)):
                return True
        
        # 后序遍历
        res.append(node)
        # 邻接结点已经遍历结束
        states[node] = State.VISITED

        return False
```

## 精选例题

### 题型一：图中是否存在环 （课程表）

### 题型二：有向无环图中的最长路径 （课程表）

### 题型三：有向无环图的拓扑排序 （课程表）

## 精选练习
