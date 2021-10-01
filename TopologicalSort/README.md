# 拓扑排序算法

作者：wu2meng3

## 拓扑排序的应用

## 拓扑排序的两个模板

* BFS
```python
class Solution(object):

	def topSort(self, graph: Dict[int, List[int]]) -> List[int]:
				"""
				@type: 邻接表表示的有向图
				@rtype: 图的结点列表
				"""
        import collections
				# 计算入度
        inDegree = self.getInDegree(graph)

        # 将入度为0的结点放入队列 queue 和哈希集合 visited 防止重复遍历
        startNodeList = [ node for node in graph if inDegree[node] == 0]
        # 用 deque 实现队列
        queue = collections.deque(startNodeList)
        visited = set(startNodeList)
        # 保存结果的数组
        topoOrder = []

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

        return topoOrder
```

* DFS

## 精选例题

### 题型一：有向无环图中的最长路径 （课程表）

## 精选练习
