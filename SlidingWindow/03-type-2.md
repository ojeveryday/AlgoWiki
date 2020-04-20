## 类型 2：窗口大小不随指针移动而改变

例题：「LeetCode」 第 219 题：[存在重复元素 II](https://leetcode-cn.com/problems/contains-duplicate-ii/)

> 给定一个整数数组和一个整数 k，判断数组中是否存在两个不同的索引 i 和 j，使得 nums [i] = nums [j]，并且 i 和 j 的差的 绝对值 至多为 k。
>
> 示例 1:
>
> 输入: nums = [1,2,3,1], k = 3
> 输出: true
>
> 示例 2：
>
> 输入: nums = [1,2,3,1,2,3], k = 2
> 输出: false

### 思路

这道题乍一看好像和滑动窗口没关系，但我们可以发现这是一个数组问题，需要频繁地使用索引（指针），而且有成立条件。因此我们可以将题目转换一下，变为：

* 给定一个整数数组和大小为 k 的窗口，判断窗口中是否能存在重复元素。

这样一来，本题就变成了一个窗口大小不变的滑动窗口问题，思路也不难想了：

* 定义一个长度最大为 k 的窗口；
* 若某元素不在窗口中，则将此元素加入窗口；若此时窗口超过了最大长度，则将先加进来的元素移出窗口；
* 若某元素已在窗口中，返回 True ；若遍历整个数组后，窗口中仍没有重复元素，返回 False

python 代码：

```python
class Solution:
    def containsNearbyDuplicate(self, nums: List[int], k: int) -> bool:
        window = set()  # 使用一个集合表示窗口

        for i in range(len(nums)):
            if nums[i] in window:
                return True

            window.add(nums[i])

            if len(window) > k:
                window.remove(nums[i-k])  # 移出最先加进来的元素

        return False
```
