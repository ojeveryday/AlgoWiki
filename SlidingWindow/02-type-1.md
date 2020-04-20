## 类型 1：窗口大小随指针移动而改变（常见）

例题：「LeetCode」 第 209 题：[长度最小的子数组](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)

> 给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的连续子数组。如果不存在符合条件的连续子数组，返回 0。
>
> 示例: 
>
> 输入: s = 7, nums = [2,3,1,2,4,3]
> 输出: 2
> 解释: 子数组 [4,3] 是该条件下的长度最小的连续子数组。

**面对一个问题，如果我们一时想不到好的解法，可以先从暴力解入手。本题暴力解思路如下**：

* 遍历由索引 i 到索引 j 的所有的连续子数组  `nums[i...j]`

* 计算子数组的和 sum，验证  `sum >= s`

* 对于所有满足条件的解，找出长度最小的解


可以看出，因为子数组的长度可以由 n 到 1，所以本解法的时间复杂度为：O (n^3)

### 暴力解的问题

我们可以很快发现，如果我们可以知道 `nums[i...j] `的值，那 `nums[i...j-1]` 的值是不是早已被计算过了呢？因此，**暴力解的最大问题，就是存在大量重复的运算**

### 使用滑动窗口，避免重复运算

为了使每次计算都有意义，我们可以定义一个左指针 `lp` ，一个右指针 `rp`  ，而 `nums[lp...rp]` 就是一个「窗口」。应用窗口的思路如下：

* 若窗口间的元素之和小于 s ，即 `sum(nums[lp...rp]) < s` ，则让右指针滑动一位，即 `rp+1` ，使得窗口间元素之和变大；
* 若窗口间的元素之和大于等于于 s ，即 `sum(nums[lp...rp]) >= s` ，我们记录此时窗口的长度，并让左指针滑动一位，即 `lp+1` ，使得窗口间元素之和变小，若此时的窗口仍满足条件，则再记录窗口的长度，直到窗口不满足条件；
* 返回记录的窗口中，最小的窗口长度

python 代码：

```python
class Solution:
    def minSubArrayLen(self, s: int, nums) -> int:
        lp = 0  # 左指针
        rp = 0  # 右指针
        table = []  # 存放所有满足条件的子数组长度
        total = 0  # 记录满足条件的窗口长度
        nlen = len(nums)
        
        while rp < nlen:
            total += nums[rp]
            while total >= s:  # 当窗口满足条件时，不断移动左指针，直到窗口不满足条件
                table.append(rp - lp + 1)
                total -= nums[lp]
                lp += 1
            rp += 1

        return min(table) if table else 0  # 如果找不到满足条件的窗口，返回0
```

我们可以发现，最后需要返回的其实只有一个值，并不需要记录所有满足条件的窗口长度，因此代码在空间上可以优化，代码如下：

```python
class Solution:
    def minSubArrayLen(self, s: int, nums) -> int:
        nlen = len(nums)
        res = nlen+1  # 将最小长度初始化为一个不可能的值
        lp = 0
        rp = 0
        total = 0  # 记录数组之和
        while rp < nlen:
            total += nums[rp]
            while total >= s:
                res = min(res, rp-lp+1)  # 始终存储最小的长度
                total -= nums[lp]
                lp += 1
            rp += 1

        return res if res < nlen+1 else 0
```

**注意：**

* 对于窗口，我们必须明确左右指针的具体含义，即指针指向的元素属不属于这个窗口。在本题中，我们定义的是 `[lp...rp]` 左闭右闭的窗口，即左指针与右指针指向的元素都属于窗口，各位也可以尝试定义 `[lp...rp)` 或 `(lp...rp]` 的窗口
