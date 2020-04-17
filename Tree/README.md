## LeetCode 中 Tree 的题型总结以及 Java 代码模板

---
### Tree 的定义：
 Tree 是 Graph 的一种：
 满足以下三个条件中的任意两个的图就是树， 设图` G `中有` n `个节点和` m `条边。
 1. ` G `是一个联通图。
 2. ` G `中没有环。
 3. ` m `=` n - 1 `。
 
 LeetCode 中大部分的树都是有 root 节点的，所以这里仅讨论有根树的题型总结以及解答模板。

 更多树的定义和分类可以参考 [https://oi-wiki.org/graph/tree-basic/](https://oi-wiki.org/graph/tree-basic/)

### 解题核心思路： 
 
 绝大部分的有根树的题目可以用递归来解答。
 用到递归，就需要明白递归的三要素：递归的定义，拆解和出口。

1. 树的遍历
2. 树的构造
3. 二叉树
4. 树的路径
5. 树的祖先后代
6. 树与图之间的转化 

### 目录：

 1. 树的遍历
  1.1. 树的前序遍历
  1.2. 树的中序遍历
  1.3. 树的后序遍历 
  1.4. 树的层序遍历
 2. 树的构造
  2.1. 根据遍历结果构造树
  2.2. 构造二叉搜索树
  2.3. 删除树的节点
 3. 二叉树
  3.1 二叉搜索树
 4. 树的路径
  4.1. 树的深度
  4.2. 树的直径
  4.3. 树的最长路径
 5. 树的祖先后代
  5.1. 最低公共祖先 
 6. 树与图之间的转化 

---



---

#### Latex

行内 Latex 支持：$O(n^2)$。

$$m \leq n,\  i < m \implies j = \frac{m+n+1}{2} - i > \frac{m+n+1}{2} - m \geq \frac{2m+1}{2} - m \geq 0$$

> 需要独行 Latex，请使用 `$$` 包围。

---

#### 代码

使用一段 `行内代码`。

> 代码片段

```java
public class Main {
    public static void main(String[] args) {
        System.out.print("你好力扣");
    }
}
```

> 组合代码模块

```java [solution1-Java 答案]
public class Main {
    public static void main(String[] args) {
        System.out.print("你好力扣");
    }
}
```

---

| 题号 | 标题 | 难易度 | 类型 | 类型2 |
| :--- | :---:| :---: | :---: | :---: |
| 124 | [二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/) | 困难 | 路径 | 后序遍历 |
