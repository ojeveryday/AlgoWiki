# 精选例题

这里给出两道经典例题的思路，熟练掌握思想之后，「力扣」中等难度的 Trie 类型题应该没有问题了。

## 例题1

***

给定 $N$ 个字符串 $S1,S2…SN$ ，接下来进行 $M$ 次询问，每次询问给定一个字符串 $T$ ，求 $S1～SN$ 中有多少个字符串是 $T$ 的前缀。输入字符串的总长度不超过 $10^6$ ，仅包含小写字母。

输入格式

第一行输入两个整数 $N$ ，$M$ 。

接下来 $N$ 行每行输入一个字符串 $Si$。

接下来 $M$ 行每行一个字符串 $T$ 用以询问。

输出格式

对于每个询问，输出一个整数表示答案。

每个答案占一行。

***

把这 $N$ 个字符串插入一棵 Trie 树，Trie 树的每个节点上存储一个整数 $cnt$ ，记录该节点是多少个字符串的末尾节点（为了处理插入重复字符串的情况，这里要记录个数，而不能只做结尾标记），对于每个询问，在 Trie 树中检索要查询的串的每个子串，在检索的过程中累加每次子串查询得到的 $cnt$ 值，最后得到最终答案

***

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e6 + 10;
int son[N][26], cnt[N], idx;

void insert(string str) {
    int p = 0;
    for (int i = 0; str[i]; ++i) {
        int temp = str[i] - 'a';
        if (!son[p][temp]) son[p][temp] = ++idx;
        p = son[p][temp];
    }
    cnt[p]++;
}

int query(string str) {
    int p = 0;
    for (int i = 0; str[i]; ++i) {
        int temp = str[i] - 'a';
        if (!son[p][temp]) return 0;
        p = son[p][temp];
    }
    return cnt[p];
}

int main() {
    int n, m;
    cin >> n >> m;
    for (int i = 0; i < n; i++) {
        string s;
        cin >> s;
        insert(s);
    }
    while (m--) {
        string s;
        cin >> s;
        int ans = 0;
        for (int i = 0; s[i]; i++) {
            ans += query(s.substr(0, i + 1));
        }
        cout << ans << endl;
    }
    return 0;
}
```

## 练习题

「力扣」第 648 题：[单词替换](https://leetcode-cn.com/problems/replace-words/)。

## 例题2

「力扣」第 421 题：[数组中两个数的最大异或值](https://leetcode-cn.com/problems/maximum-xor-of-two-numbers-in-an-array/)。

***

在给定的 $N$ 个整数 $A1，A2……AN$ 中选出两个进行 $xor$（异或）运算，得到的结果最大是多少？

输入格式

第一行输入一个整数 $N$ 。

第二行输入 $N$ 个整数 $A1～AN$。

输出格式

输出一个整数表示答案。

数据范围

$1≤N≤10^5, 0≤Ai<2^{31}$

***

我们首先想到的是朴素算法，暴力的在所有数中枚举两个数使这两个数做 $XOR$ 运算的值最大，但是由于数据范围过大，这种算法是超时的，所以我们需要考虑其他思路。  

朴素的算法是两层嵌套的 ``for`` 循环，我们可以优化朴素算法，借助 Trie 把第二层 ``for`` 循环从 ``O(N)`` 优化到 $O(31)$ ，这样其时间复杂度就由 $O(N^2)$ 变成了 $O(31*N)$。

我们可以把每个整数看作其二进制位数为 31 的 ``01`` 字符串，当数值较小时在前补 ``0``（因为题目要求所有数大于 ``0`` ，而最高位存储的是符号位，所以最高位一定为 ``0`` ，做异或运算无意义，因此为 31 位），我们把每个数的二进制串插入到 Tire 中（其中叶子节点为最低位），接下来假如第一重 ``for`` 循环枚举到 ``Ai`` ，那我们需要找到与 ``Ai`` 对应的整数，使其与 ``Ai`` 做异或运算的值最大，当我们从最高位开始找 ``Ai`` 对应的整数时，因为 ``XOR`` 运算“相同得 ``0`` ，不同得 ``1`` ”的性质，我们每次都希望找到与 ``Ai`` 对应位的相反的数（为 ``0`` 找 ``1`` ，为 ``1`` 找 ``0`` ），这样才能使两个数对应位做异或运算后为 ``1`` ，得到的值才尽可能大，如果“与 ``Ai`` 的当前为相反的字符指针”指向空节点，则只好访问与 ``Ai`` 当前位相同的字符指针，这样就可以找到所有数中和 ``Ai`` 做异或运算值最大的数。 

![](https://ae01.alicdn.com/kf/H49d1b2d9a173404ab50a08f5f004d0b84.jpg)

这就是这道题的思路

***

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 1e5 + 10;
const int M = 3100000;

int son[M][2], a[N], idx;

void insert(int x) {
    int p = 0;
    for (int i = 30; ~i; --i) {
        int u = (x >> i) & 1;
        if (!son[p][u]) son[p][u] = ++idx;
        p = son[p][u];
    }
}

int search(int x) {
    int p = 0, ans = 0;
    for (int i = 30; ~i; --i) {
        int u = (x >> i) & 1;
        if (son[p][!u]) {
            ans += (1 << i);
            p = son[p][!u];
        } else {
            p = son[p][u];
        }
    }
    return ans;
}

int main() {
    int n;
    cin >> n;
    for (int i = 0; i < n; i++) {
        cin >> a[i];
        insert(a[i]);
    }
    
    int ans = 0;
    
    for (int i = 0; i < n; i++) {
        ans = max(ans, search(a[i]));
    }
    
    cout << ans << endl;
    return 0;
}
```

## 练习题

「力扣」第 745 题：[前缀和后缀搜索](https://leetcode-cn.com/problems/prefix-and-suffix-search/)。

这道题有一定难度，但是思想与本题一样，既然数字可以灵活转换为二进制存储，那么这道题你有什么想法呢？