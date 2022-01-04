# AlgoWiki

**本仓库用来记录各类算法题的常用模板、思路，能让大家更快的刷题！**

**在线阅读**：https://ojeveryday.github.io/AlgoWiki/#/



参加这个开源项目，你将获得：

1. ⏳学习和总结了常用的模板；
2. 🚀掌握一类题目的解题技巧；
3. 📒锻炼了自己的文档书写能力；
4. 🏃强化了Git和多人协作方法；
5. 👨‍🔬‍‍两位评审对你的文章进行校阅。



本仓库会说明每个人的贡献哦~ 快加入它✊

**加入方式**：发邮件至fuxuemingzhu#163.com（#换成@），说明自己的联系方式和认领专题。

## 书写规范

- 文档规范：

1. 力扣有一个书写文章的规范 https://leetcode-cn.com/circle/article/hipGkf/

   **文末有一个「中文文案指北」挺重要的，一定要认真看；**

   

- 代码规范：

  原则：如果业内有编码规范，需要严格参考编码规范编写代码，避免代码个人风格化。

  Java 语言参考《阿里巴巴 Java 开发手册》（https://github.com/alibaba/p3c）。

  以下没有列出的语言，请各位老师自行根据掌握的知识修改。

  C++ 语言：https://google.github.io/styleguide/cppguide.html

1. 写「参考代码」或者是「模板代码」的时候，用编辑器写（Java 可以用 IDEA，Python 用 PyCharm），都会有格式化的功能，这样最省心，不用自己去调格式；
2. for 和 if 后面的代码，即使只有一行都换行和加括号；目前争议最大的是左括号之前是否换行，不要使用自己的习惯，应该遵守行业编码规范；
3. 声明变量的时候，同样类型的变量，不管有几个，必须一行声明一个；
4. 注释不要写尾注释，注释都是单独一行，// 后面加上空格；
5. “” 都用统一都用「」，英文和数字混在中文里面的，前后要加空格；
6. 参考代码写必要的注释，如果代码太长应该想办法抽取成为私有函数，保证主线逻辑要清楚。并且主线函数在上方，私有函数在下方；
7. （Java）严格使用访问控制符；
8. 视情况使用全局变量；
9. 每道问题后面，除了回溯算法需要剪枝的问题，尽量写复杂度分析。如果是用到主定理的，需要简单说明推导过程。
10. nums[index++] 这种写法应该拆成两行，遵循一行只做一件事情。

## 协作规范

1. 每个模块有个主写，负责主要思路的书写，以及贡献一种语言。
2. 有Backup负责帮助其他语言的补充，也避免有事情怕耽误了更新。

**分支介绍**：

- master 分支是主干，做项目发布，谨慎修改；

- docsify 分支是预发布分支，在该分支进行预览；

- 其他分支为内容编写分支。

**分支操作**：

1. 本地新建**分支命名**以 功能名+用户名 的形式，比如 DFS_fuxuemingzhu。
2. 在做 push 操作时，应该按照下面的顺序进行操作：

```shell
git checkout docsify
git pull
git checkout DFS_fuxuemingzhu
git merge docsify
git push
```

3. 新建 pull requests 从 DFS_fuxuemingzhu  到 docsify 分支，并进行代码评审。
4. 如果评审通过，则合并到 docsify 分支，本地进行预览：

```shell
git checkout docsify
git pull
docsify serve .
```

5. 确定没有问题，再新建 pull requests 从 docsify  到 master 分支。

6. 合并到主干后，会自动发布，线上地址 https://ojeveryday.github.io/AlgoWiki/#/。

## 任务认领

欢迎大家认领任务，共同贡献。

| 专题               | 认领情况 | 完成情况 |
| ------------------ | -------- | -------- |
| BFS                | 已认领 |          |
| BitManipulation    | 已认领 |          |
| DynamicProgramming | 已认领 |          |
| Heap               | 已认领 |          |
| Queue              | 已认领 |          |
| Sort               | 已认领 |          |
| Tree               | 已认领 |          |
| Backtracking       | 已认领 |          |
| DFS                | 已认领 |          |
| Graph              | 已认领 |          |
| LinkedList         | 已认领 |          |
| Stack              | 已认领 |          |
| TwoPointers        | 已认领 |          |
| BinarySearch       | liweiwei1419，OneDirection9 | 已完成 |
| Divide&Conquer     | 已认领 |          |
| HashTable          | 已认领 |          |
| Math               | 已认领 |          |
| SlidingWindow      | 已认领 |          |
| String             | 已认领 |          |
| UnionFind          | 已认领 |          |
| SegmentTree | 已认领 |          |
| Array |  | |
| TopologicalSort | 已认领，wu2meng3 | |
| Trie |  | |
| BinaryIndexedTree |  | |
| BinarySearchTree |  | |
| Recursion |  | |
| Brainteaser |  | |
| Memoization |  | |
| Minimax |  | |
| ReservoirSampling |  | |
| OrderedMap |  | |
| Geometry |  | |
| Random |  | |
| RejectionSampling |  | |
| LineSweep |  | |
| RollingHash |  | |
| SuffixArray |  | |
| Design |  | |
| Greedy |  | |
| | | |