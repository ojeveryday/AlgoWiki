# 进阶模板

基础模板里面最坏情况树会退化成链，采用**路径压缩（Path Compression）**进行优化，将 `x` 到根节点的所有节点全部指向根，`x`  为当前调用 `Find(x)` 函数的节点。这样的好处是会让树变得扁平。

## 例题

[「洛谷」P 1551 亲戚](https://www.luogu.com.cn/problem/P1551)。

## 代码

与基础模板的区别主要是 `Find(x)` 函数。

```cpp
#include <iostream>
#include <vector>

using namespace std;

class UnionFind {
   public:
    vector<int> p;
    UnionFind(int n) {
        // 集合的代表元素，parent 数组
        p = vector<int>(n, 0);
        // 初始时每个集合的代表元素就是自身
        for (int i = 0; i < n; ++i) {
            p[i] = i;
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
            p[px] = py;
        }
    }
};

int main() {
    freopen("1.txt", "r", stdin);
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

## 分析

### 初始状态

![init](02-init.png)

给定 6 个人，一开始我们并不知道他们的亲戚关系，所以每个人都是一个单独的集合，每个集合的代表就是它自己。

### 合并

依次 `1 2`，`1 5`，`3 4`，`5 2` 进行合并之后得到：

![pair1](03-pair1.png)

#### 亲戚关系 `1 3`

执行 `Union(1, 3)` 之后：

![pair2](03-pair2.png)

执行 `Union(1, 3)` 时，需要先执行 `Find(1)` 和 `Find(3)`。

`1` 的关系网为：`1 -> 2 -> 3`。代码执行路径为 `Find(1) { p[1] = Find(2); } -> Find(2) { p[2] = Find(3); } -> Find(3) { return 3; }`。所以执行之后 `1` 的父节点由 `2` 更改为 `3`。这样下次再调用 `Find(1)` 时就不需要再访问 `2`，压缩了路径。

可以思考一下，如果像基础模板里树退化成链之后，执行一次 `Find(1)` 会变成什么？

> 执行一次 `Find(1)` 之后，`1 2 3 4 5`所有节点的父节点都会变成 `6`，树的高度变成`2`。

## 时间复杂度

可以先比较下两个模板的评测结果，直观的感受下时间复杂度的变化。

路径压缩版本的并查集时间复杂度是 **`O(logn)`**，`n` 是节点数。

具体证明参考：[R. Tarjan and J. van Leeuwen. Worst-case Analysis of Set Union Algorithms. J. ACM, Vol. 31, No. 2, April 1984, pp. 245-281.](https://www.researchgate.net/publication/220430653_Worst-case_Analysis_of_Set_Union_Algorithms)。