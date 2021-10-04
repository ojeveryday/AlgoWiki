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

该算法也叫 [Kahn 算法](https://en.wikipedia.org/wiki/Topological_sorting#Kahn's_algorithm)。首先，我们需要介绍**入度**（ indegree ）的概念。在有向图当中，一个结点的入度表示有多少条边指向它。对应的还有一个概念叫**出度** （ outdegree ），代表从一个结点出发有多少条边，但是在拓扑排序当中我们只会用到入度。入度为 0 的点的集合将作为 BFS 的起点。如果没有入度为零的点，则无法进行拓扑排序。BFS 由先进先出 （First In, First Out, FIFO）的队列 （ queue ）数据结构来实现。BFS 算法实现拓扑排序的文字描述如下：

1. 遍历邻接表中所有结点和边，计算每个结点的入度。
2. 从入度为 0 的结点出发进行 BFS ，将入度为 0 的结点放入输出列表。遍历入度为 0 结点的邻接结点时将这些邻接结点的入度减 1（相当于删掉已经遍历过的边），如果其入度是 0 ，则放入队列。

伪代码如下
```python
L ← 输出列表
Q ← 包含所有入度为 0 结点的队列
while ( Q 不为空 ) do
    n 出列
    将 n 放入 L
    for ( n 的所有邻接结点 m ) do
        删除该边，m 的入度减 1
        if ( m 入度为 0 ) then
            m 入列

if （图中存在边） then
    # 存在环，不可以拓扑排序
    return None
else
    return L
```

之前说过拓扑排序结果并不唯一，按照字典序 （ lexicographical order ）可以将所有可能的输出结果进行排序。如果需要最大或者最小结果，可以将 Kahn 算法中的队列替换成最大堆/最小堆实现的优先队列即可，具体实现参考[精选例题X]。

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

class Solution(object):

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

### 题型一：课程表
例题 1: 「力扣」 第 207 题：[课程表（Course Schedule）](https://leetcode-cn.com/problems/course-schedule/)

例题 2: 「力扣」 第 210 题：[课程表 II（Course Schedule II）](https://leetcode-cn.com/problems/course-schedule-ii/)

> 现在你总共有 `numCourses` 门课需要选，记为 `0`到 `numCourses - 1`。给你一个数组 `prerequisites` ，其中 `prerequisites[i] = [a$_i$, b$_i$]` ，表示在选修课程 `a_i` 前**必须**先选修 `b_i` 。
> 返回你为了学完所有课程所安排的学习顺序。可能会有多个正确的顺序，你只要返回**任意一种**就可以了。如果不可能完成所有课程，返回**一个空数组**。

**分析**
* 这两道题例题就是最经典的拓扑排序应用，我们直接套用上述模板即可。

BFS 答案

```python
class Solution(object):

    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        graph = self.buildGraph(numCourses, prerequisites)

        inDegree = {i: 0 for i in range(numCourses)}

        for prerequisite in prerequisites:
            inDegree[prerequisite[0]] += 1

        startNodeList = [i for i in range(numCourses) if inDegree[i] == 0]
        queue = collections.deque(startNodeList)
        visited = set(startNodeList)
        topoOrder = []
        count = numCourses
        while queue:
            node = queue.popleft()
            topoOrder.append(node)
            count -= 1

            for child in graph[node]:
                if child in visited:
                    continue

                inDegree[child] -= 1

                if inDegree[child] == 0:
                    queue.append(child)
                    visited.add(child)

        if count != 0:
            return []

        return topoOrder

    def buildGraph(self, N: int, relations: List[List[int]]):
        graph = {i: [] for i in range(N)}

        for relation in relations:
            graph[relation[1]].append(relation[0])

        return graph
```

DFS 答案

```python
from enum import Enum

class State(Enum):
    UNVISITED = 0
    VISITING = 1
    VISITED = 2


class Solution(object):

    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        graph = self.buildGraph(numCourses, prerequisites)

        states = {node: State.UNVISITED for node in graph}
        res = []

        for node in graph:
            if (self.hasCycle(graph, node, states, res)):
                return []

        return res[::-1]

    def buildGraph(self, N: int, relations: List[List[int]]) -> Dict[int, List[int]]:
        graph = {i: [] for i in range(N)}

        for relation in relations:
            graph[relation[1]].append(relation[0])

        return graph

    def hasCycle(self, graph: Dict[int, List[int]], node: int, states: List[State], res: List[int]) -> bool:
        if states[node] == State.VISITING:
            return True

        if states[node] == State.VISITED:
            return False

        states[node] = State.VISITING

        for child in graph[node]:
            if (self.hasCycle(graph, child, states, res)):
                return True

        res.append(node)
        states[node] = State.VISITED

        return False
```

**复杂度分析**：
* 时间复杂度：$O(|V|+|E|)$，其中 $V$ 是所有字符的集合，$E$ 是所有字典序线索的集合。
* 空间复杂度：$O(|V|+|E|)$

### 题型二：最大/最小字典序拓扑排序

例题 3: Lintcode 第892题，[外星人字典（ Alien Dictionary）](https://www.lintcode.com/problem/892/)

> 有一种新的使用拉丁字母的外来语言。但是，你不知道字母之间的顺序。你会从词典中收到一个非空的单词列表，其中的单词在这种新语言的规则下按字典顺序排序。请推导出这种语言的字母顺序。
> * 假设所有字母都是小写。
> * 如果单词 a 是单词 b 的前缀且 b 出现在 a 之前，那么这个顺序是无效的。
> * 如果顺序是无效的，则返回空字符串。
> * 这里可能有多个有效的字母顺序，返回以正常字典顺序看来最小的。

**分析**：
* 字典序可以看成一幅图，结点是所有出现过的拉丁字母，有向边 $(u, v)$ 代表在字典序当中字母 $u$ 排在字母 $v$ 前面。
* 通过比较相邻的单词，可以得到关于该字典序的线索，将其作为边加入图。
* 为了返回用正常英文字典顺序看来最小的结果，用 BFS 遍历的时候，需要保证每次出列的字符都是队列中最小的。

以下是 BFS + 最小堆实现的代码。

```python
class Solution(object):

    def alienOrder(self, words: List[str]):
        # 比较相邻单词构造图
        graph = self.buildGraph(words)
        if not graph:
            return ""

        # 如果存在环, 则不存在有效的字典序
        topoOrder = self.topoSort_BFS_PriorityQueue(graph)
        if not topoOrder:
            return ""

        return ''.join(topoOrder)

    def buildGraph(self, words: List[str]):

        graph = {char: [] for word in words for char in word}

        for i in range(1, len(words)):
            w1 = words[i-1]
            w2 = words[i]
            for i in range(min(len(w1), len(w2))):
                if w1[i] != w2[i]:
                    graph[w1[i]].append(w2[i])
                    break

                #  如果w2是w1的前缀且w1出现在w2之前，则不存在有效的字典序
                if i == min(len(w1), len(w2))-1 and len(w1) > len(w2):
                    return None

        return graph

    def topoSort_BFS_PriorityQueue(self, graph: Dict[str, List[str]]):
        inDegree = {char: 0 for char in graph}

        for char, neighbors in graph.items():
            for neighbor in neighbors:
                inDegree[neighbor] += 1

        pq = [char for char in graph.keys() if inDegree[char] == 0]

        # 最小堆实现的优先队列
        heapq.heapify(pq)
        visited = set(pq)
        topoOrder = []
        count = len(inDegree)

        while pq:
            node = heapq.heappop(pq)
            topoOrder.append(node)
            count -= 1

            for child in graph[node]:
                if child in visited:
                    continue
                inDegree[child] -= 1
                if inDegree[child] == 0:
                    heapq.heappush(pq, child)
                    visited.add(child)

        # 存在环，不存在有效的字典序
        if count != 0:
            return []

        return topoOrder
```

**复杂度分析**：

* 时间复杂度：$O(|V|+|E|)$，其中 $V$ 是所有字符的集合，$E$ 是所有字典序线索的集合。
* 空间复杂度：$O(|V|+|E|)$。

## 精选练习

| 题目                                                         | 类型                         |
| ------------------------------------------------------------ | ---------------------------- |
| [LC 1136. 平行课程](https://leetcode-cn.com/problems/parallel-courses/) | 中等，必做                   |
| [LC 310. 最小高度树](https://leetcode-cn.com/problems/minimum-height-trees/) | 中等， 必做 |
| [LC 329. 矩阵中的最长递增路径](https://leetcode-cn.com/problems/longest-increasing-path-in-a-matrix/) | 困难，必做 |
| [CF 1385E. Directing Edges](https://codeforces.com/problemset/problem/1385/E) |  |


# 参考
* [Topological sorting @ Wikipedia](https://en.wikipedia.org/wiki/Topological_sorting#Kahn's_algorithm)
* https://oi-wiki.org/graph/topo/
* 《算法导论》第 22 章
