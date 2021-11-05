# 前缀和模板一

例题 1：「力扣」的 [1480. 一维数组的动态和](https://leetcode-cn.com/problems/running-sum-of-1d-array/)。

> 给你一个数组 `nums` 。数组「动态和」的计算公式为：`preSum[i] = sum(nums[0]…nums[i])` 。
>
> 请返回 `nums` 的动态和。
>
> 示例 ：
>
> 输入：`nums = [1,2,3,4]`
> 输出：`[1,3,6,10]`
> 解释：动态和计算过程为 `[1, 1+2, 1+2+3, 1+2+3+4]`  。

这个题让我们求 `preSum[i] = sum(nums[0]…nums[i])`，如果你没有了解过「前缀和」，可能会写出两重循环：每个 `preSum[i]`，累加从 $0$ 位置到 $i$ 位置的 `nums[i]`。即，写出下面的代码：



<!-- tabs:start -->

#### **Java**

```java
class Solution {
    public int[] runningSum(int[] nums) {
        int N = nums.length;
        int[] preSum = new int[N];
        for (int i = 0; i < N; ++i) {
            int sum = 0;
            for (int j = 0; j <= i; ++j) {
                sum += nums[j];
            }
            preSum[i] = sum;
        }
        return preSum;
    }
}
```

#### **C++**

```c++
vector<int> runningSum(vector<int>& nums) {
    const int N = nums.size();
    vector<int> preSum(N, 0);
    for (int i = 0; i < N; ++i) {
        int sum = 0;
        for (int j = 0; j <= i; ++j) {
            sum += nums[j];
        }
        preSum[i] = sum;
    }
    return preSum;
}
```
#### **Python**

```python
class Solution(object):
    def runningSum(self, nums):
        N = len(nums)
        preSum = [0] * N
        for i in range(N):
            curSum = 0
            for j in range(i + 1):
                curSum += nums[j]
            preSum[i] = curSum
        return preSum
```

<!-- tabs:end -->



两重循环的时间复杂度是 $O(N^2)$，效率比较低。

其实我们只要稍微转变一下思路，就发现没必要用两重循环。我们使用类似「动态规划」的思想，从一个小问题推导出更大的问题：

当已知 `preSum[i]` 是数组前 $i$ 项的和，那么数组的前 $i + 1$ 项的和 `preSum[i + 1] = preSum[i] + nums[i + 1]`。

一个简单的转换，让我们可以省去内层的 `for` 循环。

于是我们就得到了「前缀和」数组——

>
> 「前缀和」 就是从 `nums` 数组中的第  0 位置开始，累加到第 $i$ 位置的结果，我们常把这个结果保存到数组 `preSum` 中，记为  `preSum[i]`。

写出的代码如下：



<!-- tabs:start -->

#### **Java**

```java
class Solution {
    public int[] runningSum(int[] nums) {
        int N = nums.length;
        int[] preSum = new int[N];
        for (int i = 0; i < N; ++i) {
            if (i == 0) {
                preSum[i] = nums[i];
            } else {
                preSum[i] = preSum[i - 1] + nums[i];
            }
        }
        return preSum;
    }
}
```

#### **C++**


```cpp
vector<int> runningSum(vector<int>& nums) {
    const int N = nums.size();
    vector<int> preSum(N, 0);
    for (int i = 0; i < N; ++i) {
        if (i == 0) {
            preSum[i] = nums[i];
        } else {
            preSum[i] = preSum[i - 1] + nums[i]; 
        }
    }
    return preSum;
}
```

#### **Python**

```python
class Solution(object):
    def runningSum(self, nums):
        N = len(nums)
        preSum = [0] * N
        for i in range(N):
            if i == 0:
                preSum[i] = nums[i]
            else:
                preSum[i] = preSum[i - 1] + nums[i]
        return preSum
```

<!-- tabs:end -->



- 时间复杂度：$O(N)$;
- 空间复杂度：$O(N)$。



上文是「前缀和」的基本求法。

那我们怎么用「前缀和」数组求数组的区间和呢？

根据前缀和的定义 `preSum[i]` 是数组 `nums` 的前 $i$ 项之和，所以：

1. 数组 $[0, i]$ 区间的和 = `preSum[i]`;
2. 数组 $[i, j]$ 区间的和 = `preSum[j] - preSum[i - 1]`;

![](https://picture-bed-1251805293.cos.ap-beijing.myqcloud.com/202111050833861.png)

至此，我们已经把如何求「前缀和」以及如何用「前缀和」求数组的区间和讲解清楚了。