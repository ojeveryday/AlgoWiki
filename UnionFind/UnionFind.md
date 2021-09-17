# 并查集

作者：OneDirection 审核：liweiwei1419；zerotrac。

并查集（Disjoint set，Union-Find）是一种用来管理元素分组情况的数据结构。并查集的一种高效实现是：**使用树的根节点「代表」一个集合**，同一棵树上的所有节点属于一个集合。只要两个元素的根节点相同，就视为属于同一个集合。使用树的根节点代表一个集合的方法，称之为「代表元」法。

- **初始化（Init）**：将每个元素所在集合初始化为其自身。

- **合并（Union）**：将两个元素所属的集合合并为一个集合。
- **查找（Find）**：查找元素所在的集合，即根节点。

# 应用

- 最小生成树：Kruskal 算法

- 图的连通分量

- 静态连通性

- 动态连通性：判断图的最早连通时间

# 局限

- 不支持拆分（split）操作：任何节点一旦成为其他节点的子节点后，将永远不可能再成为树根。

- 集合必须是不相交的（disjoint）：同一个元素不能属于多个集合。

- 代表元不记录集合成员信息：

    - 父节点不记录子节点的信息。

    - 查找图中某节点所在的连通分量的所有节点需要再次扫描或者维护额外信息。

# 模板

本章节提供了三个模板：

1. 模板一：实现并查集的基本功能。
2. 模板二：实现「路径压缩」优化的并查集。
3. 模板三：实现「路径压缩」与「按秩合并」优化的并查集。

模板一的实现仅仅能够完成任务，但是效率较低。在真正应用并查集的时候，需要使用一些优化操作：「路径压缩」和「按秩合并」。两者可以同时使用，一般使用其一就能够保证不错的时间效率。

 

# 基础模板

并查集需要实现 `init(n)`，`Find(x)` 以及 `Union(x, y)` 函数，分别对应初始化，查找，合并操作。

我们通过一个非常典型的问题，向大家介绍并查集的实现原理：[「洛谷」P 1551 亲戚](https://www.luogu.com.cn/problem/P1551)。

## 例题

> #### 题目背景
>
> 若某个家族人员过于庞大，要判断两个是否是亲戚，确实还很不容易，现在给出某个亲戚关系图，求任意给出的两个人是否具有亲戚关系。
>
> #### 题目描述
>
> 规定：`x` 和 `y` 是亲戚，`y` 和 `z` 是亲戚，那么 `x` 和 `z` 也是亲戚。如果 `x`，`y` 是亲戚，那么 `x` 的亲戚都是 `y` 的亲戚，`y` 的亲戚也都是 `x` 的亲戚。
>
> #### 输入格式
>
> 第一行：三个整数 `n`，`m`，`p`，（`n<=5000`，`m<=5000`，`p<=5000`），分别表示有 `n` 个人，`m` 个亲戚关系，询问 `p` 对亲戚关系。
>
> 以下 `m` 行：每行两个数 `Mi`，`Mj`，`1 <= Mi, Mj <= N`，表示 `Mi` 和 `Mj` 具有亲戚关系。
>
> 接下来 `p` 行：每行两个数 `Pi`，`Pj`，询问 `Pi` 和 `Pj` 是否具有亲戚关系。
>
> #### 输入输出样例
>
> **输入 #1**
>
> ```
> 6 5 3
> 1 2
> 1 5
> 3 4
> 5 2
> 1 3
> 1 4
> 2 3
> 5 6
> ```
>
> **输出 #1**
>
> ```
> Yes
> Yes
> No
> ```

## 分析

`Mi`、`Mj` 是亲戚关系代表可以合并 `Mi`、`Mj` 所在的集合。`Pi`、`Pj` 是否具有亲戚关系，只需判断他们是否在同一集合。

### 初始状态

![init](02-init.png)

给定 6 个人，一开始并不知道他们之间的关系，所以每个人都是一个单独的集合，每个集合的代表就是它自己。

### 合并

`1` 和 `2` 是亲戚关系，将他们合并为一个集合。合并之后，用 `2` 代表整个集合：

![pair1](02-pair1.png)



`1` 和 `5` 是亲戚关系，根据题意合并两人所在的集合。`1` 所在集合的代表是 `2`，合并之后用 `5` 代表整个集合：

![pair2](02-pair2.png)

依次执行 `Union(3, 4)`，`Union(5, 2)`，`Union(1, 3)` 之后得到如下关系图：

![pair](02-pair3.png)

### 查询

判断两个是否有亲戚关系，只需要判断两个人是否属于同一集合，即集合根节点是否相同。

**`1 4`**：`1` 所在集合的根结点为 `4`，`4` 所在集合的根结点为 `4`，所以他们属于同一个集合，输出 `Yes`。 **`2 3`**同理。

**`5 6`**：`5` 所在集合的根结点为 `4`，`6` 所在集合的根结点为 `6`，所以他们不属于同一个集合，输出 `No`。

## 代码

```cpp
#include <iostream>
#include <vector>

using namespace std;

class UnionFind {
   public:
    vector<int> parent;
    UnionFind(int n) {
        // 集合的代表元素 parent 数组
        parent.resize(n);
        // 初始时每个集合的代表元素就是自身
        for (int i = 0; i < n; ++i) {
            parent[i] = i;
        }
    }

    /* 查找 x 所在集合的代表元素，即父节点 */
    int Find(int x) {
        while (x != parent[x]) {
            // 不断循环查找根结点
            x = parent[x];
        }
        return parent[x];
    }

    /* 合并 x y 所在集合 */
    void Union(int x, int y) {
        // 查找 x y 所在集合的代表元素
        int px = Find(x), py = Find(y);
        if (px != py) {
            // 不在同一个集合，将 x 所在集合合并到 y 所在集合
            parent[px] = py;
        }
    }
};

int main() {
    int n, m, p;
    cin >> n >> m >> p;

    UnionFind *uf = new UnionFind(n + 1);

    int mi, mj;
    for (int i = 0; i < m; ++i) {
        cin >> mi >> mj;
        uf->Union(mi, mj);
    }

    int pi, pj;
    for (int i = 0; i < p; ++i) {
        cin >> pi >> pj;
        // 每个查询都要先找到集合的代表元素，然后判断
        int ppi = uf->Find(pi), ppj = uf->Find(pj);
        if (ppi == ppj) {
            cout << "Yes" << endl;
        } else {
            cout << "No" << endl;
        }
    }
}
```

评测结果：

![result](02-result.png)

## 时间复杂度

并查集的合并操作的主体是查找操作，所以只需要分析查找操作的时间复杂度。

最坏情况时，**树会退化成链**，例如当 `n=6` 时，依次执行如下操作：

1. `Union(1, 2)`
2. `Union(1, 3)`
3. `Union(1, 4)`
4. `Union(1, 5)`
5. `Union(1, 6)`

会得到如下结构：

![time complexity](02-tc.png)

每次查询节点 `1` 的时候都需要遍历整棵「树」，所以 `Find` 函数最坏时间复杂度是：$O(n)$，$n$ 为节点数。

## 空间复杂度

空间复杂度：$O(n)$，$n$ 为节点数。

## 引用

1. [可视化网址](https://www.cs.usfca.edu/~galles/JavascriptVisual/DisjointSets.html)

# 路径压缩模板

基础模板里，当查询某个节点的根节点时，要依次遍历父节点，直至根节点。最坏情况时树退化成链，每次执行需要 $O(n)$ 的时间复杂度，效率低下。

采用**路径压缩（Path Compression）**进行优化。路径压缩会在执行 `Find(x)` 函数时，将 `x` 到根节点的所有节点全部指向根。由于我们用的是代表元法，树的形态并不重要，路径压缩保证了同一棵树的根结点不变，但是树变得扁平，缩短下次查询时的查找路径。

## 例题

[「洛谷」P 1551 亲戚](https://www.luogu.com.cn/problem/P1551)。

## 分析

### 初始状态

![init](02-init.png)

给定 6 个人，一开始并不知道他们之间的关系，所以每个人都是一个单独的集合，每个集合的代表就是它自己。

### 合并

依次执行 `Union(1, 2)`，`Union(1, 5)`，`Union(3, 4)`，`Union(5, 2)` 之后得到：

![pair1](03-pair1.png)

执行 `Union(1, 3)` 之后：

![pair2](03-pair2.png)

执行 `Union(1, 3)` 时，需要先执行 `Find(1)` 和 `Find(3)`。

`1` 的关系网为：`1 -> 2 -> 5`。代码执行路径为 `Find(1) { p[1] = Find(2); } -> Find(2) { p[2] = Find(5); } -> Find(5) { return 5; }`。执行之后 `1` 的父节点由 `2` 更改为 `5`，将关系链变成 `1 -> 5`。这样下次再调用 `Find(1)` 时不需要再访问 `2`，压缩了路径。

思考一下，如果像基础模板里树退化成链之后，执行一次路径压缩版本的 `Find(1)` 会变成什么？

> 执行一次 `Find(1)` 之后，会得到如下结构：
>
> ![opt](03-opt.png)
>
> 下次再调用 `Find(1)`，`Find(2)` 等时，只需跳一步就能抵达根节点。

## 代码

与基础模板的区别主要是 `Find(x)` 函数。

```cpp
#include <iostream>
#include <vector>

using namespace std;

class UnionFind {
   public:
    vector<int> parent;
    UnionFind(int n) {
        // 集合的代表元素 parent 数组
        parent.resize(n);
        // 初始时每个集合的代表元素就是自身
        for (int i = 0; i < n; ++i) {
            parent[i] = i;
        }
    }

    /* 查找 x 所在集合的代表元素，即父节点 */
    int Find(int x) {
        if (x != parent[x]) {
            // 非集合代表元素，在递归调用返回的时候，将沿途经过的结点指向根节点
            parent[x] = Find(parent[x]);
        }
        return parent[x];
    }

    /* 合并 x y 所在集合 */
    void Union(int x, int y) {
        // 先查找 x y 所在集合的代表元素
        int px = Find(x), py = Find(y);
        if (px != py) {
            // 不在同一个集合，将 x 所在集合合并到 y 所在集合
            parent[px] = py;
        }
    }
};

int main() {
    int n, m, p;
    cin >> n >> m >> p;

    UnionFind *uf = new UnionFind(n + 1);

    int mi, mj;
    for (int i = 0; i < m; ++i) {
        cin >> mi >> mj;
        uf->Union(mi, mj);
    }

    int pi, pj;
    for (int i = 0; i < p; ++i) {
        cin >> pi >> pj;
        int ppi = uf->Find(pi), ppj = uf->Find(pj);
        if (ppi == ppj) {
            cout << "Yes" << endl;
        } else {
            cout << "No" << endl;
        }
    }
}
```

评测结果：

![result](03-result.png)

## 注意

路径压缩只优化 `x` 节点到其根节点的路径，而 **`x` 子节点的路径不会被优化**。

## 时间复杂度

可以先比较下两个模板的评测结果，直观的感受下时间复杂度的变化。

理论上，执行 $m$ 次 `Find` 操作，$n-1$ 次 `Union` 操作的时间复杂度是 **$O(m \log n)$**，其中 $m \geq n $。

**Theorem.** *[Tarjan-van Leeuwen 1984]* Starting from an empty data structure, path compression (with naive linking) performs any intermixed sequence of $m \geq n$ find and $n-1$ union operations in $O(m \log n)$ time.[1]

## 引用

[1]：[R. Tarjan and J. van Leeuwen. Worst-case Analysis of Set Union Algorithms. J. ACM, Vol. 31, No. 2, April 1984, pp. 245-281.](https://www.researchgate.net/publication/220430653_Worst-case_Analysis_of_Set_Union_Algorithms)

 

# 按秩合并模板

由于路径压缩只压缩 `x` 到其根节点的路径，而 `x` 子节点的路径不会被优化。如果每次只操作根节点，还是可能得到一颗复杂的树。比如依次执行如下操作：

1. `Union(1, 3)`
2. `Union(2, 3)`
3. `Union(4, 5)`
4. `Union(3, 6)`
5. `Union(5, 6)`

得到如下结构：

![tree1](04-tree1.png)

如果再执行 `Union(6, 7)`，其中 `7` 是一个单独的节点。按照前面两个模板的逻辑，会将 `6` 指向 `7`，导致 `6` 的子节点的深度都会加 `1`：

![tree2](04-tree2.png)

导致 `6` 的子节点到根节点的距离变长，之后寻找根节点的路径也就相应变长。虽然会有路径压缩进行优化，但是路径压缩也是需要先走完整条路径，才能进行优化。更优的方式是将 `7` 指向 `6`。

所以本模板里面加入**按秩合并**进行优化。**秩**是衡量一颗树的指标，它可以是：

- 树中节点数

- 树中节点的最大深度

下面例子中的秩为树中节点数。

## 例题

[「洛谷」P 1551 亲戚](https://www.luogu.com.cn/problem/P1551)。

## 分析

### 初始状态

![init](02-init.png)

给定 6 个人，一开始并不知道他们之间的关系，所以每个人都是一个单独的集合，每个集合的代表就是它自己。

### 合并

`1` 和 `2` 是亲戚关系，将他们合并为一个集合。他们初始时 `rank` 信息是一样的，合并之后，用 `2` 代表整个集合，同时更新 `2` 的 `rank` 信息 `rank[2] = 2`：

![pair1](02-pair1.png)

`1` 和 `5` 是亲戚关系，根据题意合并两人所在的集合。`1` 所在集合的代表是 `2`，它的 `rank` 信息是 `2`。而 `5` 的 `rank` 信息是 `1`，所以将 `5` 合并到 `1` 所在集合：

![pair2](04-pair1.png)

依次执行`Union(3, 4)`，`Union(5, 2)`，`Union(1, 3)` 进行合并之后得到如下结构：

![pair2](04-pair2.png)

## 代码

为了实现按秩合并，我们使用 `rank` 数组记录额外信息，在本例当中就是**树中节点数**。在调用 `Union(x, y)` 函数时通过比较不同集合的信息进行合并，而不是盲目的将 `x` 合并到 `y`。

```cpp
#include <iostream>
#include <vector>

using namespace std;

class UnionFind {
   public:
    vector<int> parent;
    vector<int> rank;
    UnionFind(int n) {
        // 集合的代表元素 parent 数组
        parent.resize(n);
        // 集合的节点个数 rank 数组
        rank.resize(n);
        // 初始时每个集合的代表元素就是自身
        // 初始时每个集合只有一个元素
        for (int i = 0; i < n; ++i) {
            parent[i] = i;
            rank[i] = 1;
        }
    }

    /* 查找 x 所在集合的代表元素，即父节点 */
    int Find(int x) {
        if (x != parent[x]) {
            // 非集合代表元素，在递归调用返回的时候，将沿途经过的结点指向根节点
            parent[x] = Find(parent[x]);
        }
        return parent[x];
    }

    /* 合并 x y 所在集合 */
    void Union(int x, int y) {
        // 先查找 x y 所在集合的代表元素
        int px = Find(x), py = Find(y);
        if (px != py) {
            // 不在同一个集合，将 x 所在集合合并到 y 所在集合
            if (rank[px] > rank[py]) {
                // x 所在集合比 y 所在集合节点数多
                // 将 y 所在集合合并到 x 所在集合，并更新 x 所在集合的节点数信息
                parent[py] = px;
                rank[px] += rank[py];
            } else {
                // y 所在集合节点数不小于 x 所在集合节点数
                // 将 x 所在集合合并到 y 所在集合，并更新 y 所在集合的节点数信息
                parent[px] = py;
                rank[py] += rank[px];
            }
        }
    }
};

int main() {
    int n, m, p;
    cin >> n >> m >> p;

    UnionFind *uf = new UnionFind(n + 1);

    int mi, mj;
    for (int i = 0; i < m; ++i) {
        cin >> mi >> mj;
        uf->Union(mi, mj);
    }

    int pi, pj;
    for (int i = 0; i < p; ++i) {
        cin >> pi >> pj;
        int ppi = uf->Find(pi), ppj = uf->Find(pj);
        if (ppi == ppj) {
            cout << "Yes" << endl;
        } else {
            cout << "No" << endl;
        }
    }
}
```

评测结果：

![result](04-result.png)

## 注意

**按秩合并**中只有根节点的秩信息是有效的，子节点信息是无效的。

## 时间复杂度

理论上，「路径压缩」与「按秩合并」，执行 $m$ 次 `Find` 操作，$n-1$ 次 `Union` 操作的时间复杂度是 **$O(m \space \alpha (m, n))$**，其中 $m \geq n $，$\alpha(m,n)$ 是反阿克曼函数。

反阿克曼函数是一个渐进复杂度很低的函数，通常可以认为是**常数级别的时间复杂度**。感兴趣的读者可以查阅引用。

**Theorem.** *[Tarjan 1975]* Link-by-size with path compression performs any intermixed sequence of $m \geq n$ FIND and $n-1$ UNION operations in $O(m \space \alpha(m, n))$ time, where $\alpha(m, n)$ is a functional inverse of the Ackermann function. [1, 2]

## 引用

[1]：[Efficiency of a Good But Not Linear Set Union Algorithm](http://www.e-maxx.ru/bookz/files/dsu/Efficiency%20of%20a%20Good%20But%20Not%20Linear%20Set%20Union%20Algorithm.%20Tarjan.pdf)

[2]：[UnionFind](https://www.cs.princeton.edu/courses/archive/spring13/cos423/lectures/UnionFind.pdf)

 

# 例题一

[LC 200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

## 分析

初始时岛屿数量是图中 `'1'` 的个数。如果一个位置为 `'1'`，其与上下左右相邻的 `'1'` 就可以合并为一个大的岛屿，同时岛屿数量减 `1`。最终无法合并的岛屿数量就是我们需要求的结果。

该题还可以通过 `DFS` 和 `BFS` 求解，请读者自行完成。

## 代码

```cpp
#include <iostream>
#include <vector>

using namespace std;

class UnionFind {
   public:
    vector<int> parent;
    int cnt;  // 连通分量的个数
    UnionFind(int n, int cnt) {
        parent = vector<int>(n);
        for (int i = 0; i < n; ++i) {
            parent[i] = i;
        }
        this->cnt = cnt;
    }

    int Find(int x) {
        if (x != parent[x]) {
            parent[x] = Find(parent[x]);
        }
        return parent[x];
    }

    void Union(int x, int y) {
        int px = Find(x), py = Find(y);
        if (px != py) {
            parent[px] = py;
            --cnt;
        }
    }
};

class Solution {
   public:
    int numIslands(vector<vector<char>> &grid) {
        if (grid.empty() || grid[0].empty()) return 0;

        m = grid.size();
        n = grid[0].size();
        int cnt = 0;
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (grid[i][j] == '1') {
                    ++cnt;
                }
            }
        }
        UnionFind *uf = new UnionFind(m * n, cnt);

        int direc[4][2] = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (grid[i][j] == '1') {
                    for (int d = 0; d < 4; ++d) {
                        int ni = i + direc[d][0], nj = j + direc[d][1];
                        if (ni < 0 || ni >= m || nj < 0 || nj >= n || grid[ni][nj] == '0') {
                            continue;
                        }
                        int id1 = get(i, j);
                        int id2 = get(ni, nj);
                        uf->Union(id1, id2);
                    }
                }
            }
        }
        return uf->cnt;
    }

   private:
    int m, n;
    int get(int i, int j) { 
        return i * n + j;
    }
};
```

# 例题二

[LC 947. 移除最多的同行或同列石头](https://leetcode-cn.com/problems/most-stones-removed-with-same-row-or-column/)

我们将石头放置在二维平面中的一些整数坐标点上。每个坐标点上最多只能有一块石头。

每次 move 操作都会移除一块所在行或者列上有其他石头存在的石头。

请你设计一个算法，计算最多能执行多少次 move 操作？

## 分析

将处于同一行或者同一列的石头两两相连，这样会得到一个图，互相连通的石子组成一个连通分量。在一个连通分量里，我们能找到一个最优的方法，进行 move 操作，直到最后只剩一个石头。首先，我们要知道每个石子都属于一个连通分量，同时在一个连通分量中移除石子不会影响到其他的连通分量。在有了这个前提之下，我们可以推断出，如果把连通分量作为一个生成树来看，每次都移除树中的叶子节点，重复这个操作，最后就只会剩下一个根节点。

如何使用并查集来解决这个问题？

如果考虑直接合并石头的话，当 `(a, b)` 点有石头时，我们需要遍历找到所有 `x=a` 或者 `y=b` 的石头进行合并，时间复杂度是 $O(n^2)$，开销比较大。

换个角度思考，当 `(a, b)` 这个点有石头时，相当于将 `x=a` 与 `y=b` 两条平行于坐标轴的线绑定在一起。集合的主体并不是石头，而是线。

当 `(a, b)` 这个点有石头时，进行 `Union(a, b)` 操作，为了保证每个点的唯一性，实际上使用的是 `Union(a, ~b)`。由于我们无法直接得到初始的节点数目，所以使用 `map` 来保存父节点信息。进行 `Find(x)` 操作时，如果 `x` 不在 `map` 里面，就将其加入 `map` 同时节点数加 `1`。如果 `x` 在 `map` 里面，说明之前已经添加过，不做任何操作。

## 代码

```cpp
#include <unordered_map>
#include <vector>

using namespace std;

class UnionFind {
   public:
    unordered_map<int, int> parent;
    int islands;

    UnionFind() {
        islands = 0;
    }

    int Find(int x) {
        if (!parent.count(x)) {
            parent[x] = x;
            ++islands;
        }
        if (x != parent[x]) {
            parent[x] = Find(parent[x]);
        }
        return parent[x];
    }

    void Union(int x, int y) {
        int px = Find(x), py = Find(y);
        if (px != py) {
            parent[px] = py;
            --islands;
        }
    }
};

class Solution {
   public:
    int removeStones(vector<vector<int>>& stones) {
        UnionFind* uf = new UnionFind();
        for (auto& v : stones) {
            uf->Union(v[0], ~v[1]);
        }
        return stones.size() - uf->islands;
    }
};
```

## 总结

介绍上面两个例题的目的在于：

1. 并查集里可以加入额外信息，比如统计图中连通分量的个数。
2. 并查集不一定局限于使用数组保存父节点信息，当节点数不确定时可以使用 `map`，相应的 `Find(x)` 做出修改。 

 

# 练习

| 题目                                                         | 类型                         |
| ------------------------------------------------------------ | ---------------------------- |
| [LC 547. 朋友圈](https://leetcode-cn.com/problems/friend-circles/) | 中等，必做                   |
| [LC 990. 等式方程的可满足性](https://leetcode-cn.com/problems/satisfiability-of-equality-equations/) | 中等，必做                   |
| [LC 200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/) | 中等，必做                   |
| [LC 130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/) | 中等，必做                   |
| [LC 684. 冗余连接](https://leetcode-cn.com/problems/redundant-connection/) | 中等， 必做                  |
| [LC 128. 最长连接序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/) | 困难，必做                   |
| [LC 1319. 连接网络的操作次数](https://leetcode-cn.com/problems/number-of-operations-to-make-network-connected/) | 中等，必做                   |
| [LC 399. 除法求值](https://leetcode-cn.com/problems/evaluate-division/) | 中等，必做，带权并查集       |
| [LC 952. 按公因数计算最大组件大小](https://leetcode-cn.com/problems/largest-component-size-by-common-factor/) | 困难，必做                   |
|                                                              |                              |
| [LC 685. 冗余连接 II](https://leetcode-cn.com/problems/redundant-connection-ii/) | 困难，选做                   |
| [LC 765. 情侣牵手](https://leetcode-cn.com/problems/couples-holding-hands/) | 困难，选做                   |
| [LC 959. 由斜杠划分区域](https://leetcode-cn.com/problems/regions-cut-by-slashes/) | 中等，选做                   |
| [POJ Butterfly](http://algorithm.openjudge.cn/betaexam/B/)   | 困难，选做，带偏移量的并查集 |

