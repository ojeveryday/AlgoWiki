# 高级模板

由于路径压缩只压缩 `x` 到其根节点的路径，而 `x` 子节点的路径不会被优化。如果每次只操作根节点，还是可能得到一颗复杂的树。比如依次执行如下操作：

1. `Union(1, 3)`
2. `Union(2, 3)`
3. `Union(4, 5)`
4. `Union(3, 6)`
5. `Union(5, 6)`

得到如下结构：

![tree1](04-tree1.png)

如果再执行 `Union(6, 7)`，其中 `7` 是一个单独的节点。按照基础模板以及进阶模板的逻辑，会将 `6` 指向 `7`，导致 `6` 的子节点的深度都会加 `1`：

![tree2](04-tree2.png)

导致 `6` 的子节点到根节点的距离变长，之后寻找根节点的路径也就相应变长。虽然会有路径压缩进行优化，但是路径压缩也是需要先走完整条路径，才能进行优化。更优的方式是将 `7` 指向 `6`。

所以高阶模板里面加入**按秩合并**进行优化。**秩**是衡量一颗树的指标，它可以是：

1. 树中节点数 （方便维护）
2. 树中节点的最大深度（不方便维护）

## 例题

[「洛谷」P 1551 亲戚](https://www.luogu.com.cn/problem/P1551)。

## 代码

为了实现按秩合并，我们使用 `r` 数组记录额外信息，在本例当中就是**树中节点数**。在调用 `Union(x, y)` 函数时通过比较不同集合的信息进行合并，而不是盲目的将 `x` 合并到 `y`。

```cpp
#include <iostream>
#include <vector>

using namespace std;

class UnionFind {
   public:
    vector<int> p;
    vector<int> r;
    UnionFind(int n) {
        // 集合的代表元素 parent 数组
        p = vector<int>(n, 0);
        // 集合的节点个数 rank 数组
        r = vector<int>(n, 1);
        // 初始时每个集合的代表元素就是自身
        // 初始时每个集合只有一个元素
        for (int i = 0; i < n; ++i) {
            p[i] = i;
            r[i] = 1;
        }
    }

    int Find(int x) {
        /* 查找 x 所在集合的代表元素，即父节点 */
        if (x != p[x]) {
            // 非集合代表元素，将其直接指向根节点，达到路径压缩的目的
            p[x] = Find(p[x]);
        }
        return p[x];
    }

    void Union(int x, int y) {
        /* 合并 x y 所在集合 */
        // 先查找 x y 所在集合的代表元素
        int px = Find(x), py = Find(y);
        if (px != py) {
            // 不在同一个集合，将 x 所在集合合并到 y 所在集合
            if (r[px] > r[py]) {
                // x 所在集合比 y 所在集合节点数多
                // 将 y 所在集合合并到 x 所在集合，并更新 x 所在集合的节点数信息
                p[py] = px;
                r[px] += r[py];
            } else {
                // y 所在集合节点数不小于 x 所在集合节点数
                // 将 x 所在集合合并到 y 所在集合，并更新 y 所在集合的节点数信息
                p[px] = py;
                r[py] += r[px];
            }
        }
    }
};

int main() {
    // freopen("1.txt", "r", stdin);
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

## 分析

### 初始状态

![init](02-init.png)

给定 6 个人，一开始并不知道他们之间的关系，所以每个人都是一个单独的集合，每个集合的代表就是它自己。

### 合并

#### 亲戚关系：`1 2`

执行 `Union(1, 2)` 之后：

![pair1](02-pair1.png)

`1` 和 `2` 是亲戚关系，将他们合并为一个集合。他们初始时 `rank` 信息是一样的，合并之后，用 `2` 代表整个集合，同时更新 `2` 的 `rank` 信息 `rank[2] = 2`。

#### 亲戚关系：`1 5`

执行 `Union(1, 5)` 之后：

![pair2](04-pair1.png)

`1` 和 `5` 是亲戚关系，根据题意合并两人所在的集合。`1` 所在集合的代表是 `2`，它的 `rank` 信息是 `2`。而 `5` 的 `rank` 信息是 `1`，所以将 `5` 合并到 `1` 所在集合。

#### 亲戚关系

依次对`3 4`，`5 2`，`1 3` 进行合并之后得到如下结构：

![pair2](04-pair2.png)

## 注意

**按秩合并**中只有根节点的秩信息是有效的，子节点信息是无效的。

## 时间复杂度

理论上，路径压缩+按秩合并，执行 $m$ 次 `Find` 操作，$n-1$ 次 `Union` 操作的时间复杂度是 **$O(m \space \alpha (m, n))$**，其中 $m \geq n $，$\alpha(m,n)$ 是反阿克曼函数。

**Theorem.** *[Tarjan 1975]* Link-by-size with path compression performs any intermixed sequence of $m \geq n$ FIND and $n-1$ UNION operations in $O(m \space \alpha(m, n))$ time, where $\alpha(m, n)$ is a functional inverse of the Ackermann function. [1, 2]

$\alpha(m, n)$ 是反阿克曼函数（Inverse Ackermann function），其定义如下：
$$
\alpha(m, n) = min \left\{ i \geq 1: A(i, \lfloor m/n \rfloor) \geq log_2n  \right\}
$$

其中 $A(m,n)$ 是阿克曼函数（Ackermann function）：
$$
A(m, n)=\left\{
\begin{array}{ll}
n+1 & \text { if } m=0 \\
A(m-1,1) & \text { if } m>0 \text { and } n=0 \\
A(m-1, A(m, n-1)) & \text { if } m>0 \text { and } n>0
\end{array}
\right.
$$
上面定义难以理解，换个方式定义：
$$
\alpha_{k}(n)=\left\{\begin{array}{ll}
1 & \text { if } n=1 \\
\lceil n / 2\rceil & \text { if } k=1 \\
1+\alpha_{k}\left(\alpha_{k-1}(n)\right) & \text { otherwise }
\end{array}\right.
$$
比如：

- $\alpha_1(n)=\lceil n/2 \rceil$
- $\alpha_2(n)=\lceil lg \space n \rceil$ = number of times we **divide n by two**, until we reach 1.

|               | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   | 12   | 13   | ...  | $2^{16}$ | ...  | $2^{65536}$ | ...  | $2\uparrow65536$   |
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | -------- | ---- | ----------- | ---- | ------------------ |
| $\alpha_1(n)$ | 1    | 1    | 2    | 2    | 3    | 3    | 4    | 4    | 5    | 5    | 6    | 6    | 7    | ...  | $2^{15}$ | ...  | $2^{65535}$ | ...  | huge               |
| $\alpha_2(n)$ | 1    | 1    | 2    | 2    | 3    | 3    | 3    | 3    | 4    | 4    | 4    | 4    | 4    | ...  | 16       | ...  | 65535       | ...  | $2 \uparrow 65535$ |
| $\alpha_3(n)$ | 1    | 1    | 2    | 2    | 3    | 3    | 3    | 3    | 3    | 3    | 3    | 3    | 3    | ...  | 4        | ...  | 5           | ...  | 65536              |
| $\alpha_4(n)$ | 1    | 1    | 2    | 2    | 3    | 3    | 3    | 3    | 3    | 3    | 3    | 3    | 3    | ...  | 3        | ...  | 3           | ...  | 4                  |

> $2 \uparrow65536 = \text {2^2^2^... repeat 65536 times}$

$\alpha(m, n)=min\left\{k: \alpha_k(n) \leq 3 + m/n \right\}$

可以简单地认为**常数时间复杂度**。

## 引用

[1]：[Efficiency of a Good But Not Linear Set Union Algorithm]([http://www.e-maxx.ru/bookz/files/dsu/Efficiency%20of%20a%20Good%20But%20Not%20Linear%20Set%20Union%20Algorithm.%20Tarjan.pdf](http://www.e-maxx.ru/bookz/files/dsu/Efficiency of a Good But Not Linear Set Union Algorithm. Tarjan.pdf))

[2]：[UnionFind](https://www.cs.princeton.edu/courses/archive/spring13/cos423/lectures/UnionFind.pdf)

