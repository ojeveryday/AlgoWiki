# 练习

| 题目                                                         | 类型                                           |
| ------------------------------------------------------------ | ---------------------------------------------- |
| [303. 区域和检索 - 数组不可变](https://leetcode-cn.com/problems/range-sum-query-immutable/) | 简单，必做                                     |
| [724. 寻找数组的中心下标](https://leetcode-cn.com/problems/find-pivot-index/) | 简单，必做。                                   |
| [974. 和可被 K 整除的子数组](https://leetcode-cn.com/problems/subarray-sums-divisible-by-k/) | 中等，必做，前缀和+哈希表                      |
| [238. 除自身以外数组的乘积](https://leetcode-cn.com/problems/product-of-array-except-self/) | 中等，必做，把「前缀和」的思想拓展到「前缀积」 |
| [523. 连续的子数组和](https://leetcode-cn.com/problems/continuous-subarray-sum/) | 中等，必做，「前缀和」拓展                     |
| [209. 长度最小的子数组](https://leetcode-cn.com/problems/minimum-size-subarray-sum/) | 中等，选做。结合二分查找；也可以用滑动窗口。   |
| [1248. 统计「优美子数组」](https://leetcode-cn.com/problems/count-number-of-nice-subarrays/) | 中等，选做。                                   |
|                                                              |                                                |

思考：上面我们讨论的 `nums` 数组都是不可变的，如果 `nums` 是可变的，该怎么办呢？

| 题目                                                         | 类型                                             |
| ------------------------------------------------------------ | ------------------------------------------------ |
| [307. 区域和检索 - 数组可修改](https://leetcode-cn.com/problems/range-sum-query-mutable/) | 中等，选做。「前缀和」已经无法解决，需要用线段树 |
|                                                              |                                                  |

