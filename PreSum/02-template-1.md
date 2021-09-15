# 滑动窗口模板一

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

```cpp
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
两重循环的时间复杂度是 $O(N^2)$，效率比较低。

其实我们只要稍微转变一下思路，就发现没必要用两重循环。

当已经求出 **`preSum[i] = sum(nums[0]…nums[i])`**，
那么 **`preSum[i + 1] = sum(nums[0]…nums[i]) + nums[i + 1]= runningSum[i] + nums[i + 1]`**。

一个简单的转换，让我们可以省去内层的 `for` 循环。

于是我们就得到了「前缀和」数组——

>
> 「前缀和」 就是从 `nums` 数组中的第  0 位置开始，累加到第 $i$ 位置的结果，我们常把这个结果保存到数组 `preSum` 中，记为  `preSum[i]`。

写出的代码如下：


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

- 时间复杂度：$O(N)$;

- 空间复杂度：$O(N)$。



上文是「前缀和」的基本写法。

为了防止当 `i = 0` 的时候数组越界，所以加了个 `if (i == 0)` 的判断，即 `i == 0` 时让 `preSum[i] = nums[i]`。

在刷题常用的写法中，可以去除这个 `if` 判断，详见模板二。