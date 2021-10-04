# 拓扑排序算法

作者：wu2meng3

拓扑排序是一个有向无环图所有结点的线性序列，满足以下两个条件：

1. 每个结点只出现一次。
2. 若存在一条从结点 A 指向结点 B 的路径，则在序列当中 A 出现在 B 之前。

若一幅图存在环路，则无法进行拓扑排序。另外，对于一幅图，其拓扑排序结果并不唯一。

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

## 拓扑排序的两个模板

* 宽度优先搜索：

  0. 构造图的邻接表。
  1. 遍历所有结点，计算每个结点的入度。
  2. 从入度为0的结点出发进行宽度优先搜索，将入度为0的结点放入输出列表。遍历入度为0结点的邻接结点时将其入度减一，如果入度为0，放入队列。

  * 时间复杂度：$O(|V|+|E|)$
  * 空间复杂度：$O(|V|)$

```python
import collections

class Solution(object):

	def topSort(self, graph: Dict[int, List[int]]) -> List[int]:
				"""
				@type: 邻接表表示的有向图
				@rtype: 图的结点列表
				"""
				# 计算入度
        inDegree = self.getInDegree(graph)
				
        # 将入度为0的结点放入队列 queue 和哈希集合 visited 防止重复遍历
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

* 深度优先搜索：一句话总结就是**拓扑排序就是后序遍历的倒序输出**。
	* 时间复杂度：$O(|V|+|E|)$
  * 空间复杂度：$O(|V|)$

```python
from enum import Enum

class State(Enum):
    UNVISITED = 0
    VISITING = 1
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
        if states[node] == State.VISITING:
            return True

        if states[node] == State.VISITED:
            return False
        
        # 回溯
        states[node] = State.VISITING
        
        # 递归处理子结点，将子结点加入 res 列表
        for child in node.neighbors:
            if (self.hasCycle(graph, child, states, res)):
                return True
        
        # 后序遍历
        res.append(node)
        states[node] = State.VISITED

        return False
```

## 精选例题

### 题型一：图中是否存在环 （课程表）

### 题型二：有向无环图中的最长路径 （课程表）

### 题型三：有向无环图的拓扑排序 （课程表）

## 精选练习
