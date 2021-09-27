# 前缀和的应用

## 求数组的区间和

利用 `preSum` 数组，可以在 $O(1)$ 的时间内快速求出 `nums`  任意区间 $[i, j]$ (两端都包含) 内的所有元素之和。

计算公式为： 

> $sum(i, j) = preSum[j + 1] - preSum[i]$


什么原理呢？其实就是消除公共部分即 $[0, i-1]$ 部分的和，那么就能得到 $[i, j]$ 部分的区间和。


注意上面的式子中，使用的是 `preSum[j + 1]` 和 `preSum[i]`，原因是：

- `preSum[j + 1]` 表示的是 `nums` 数组中 $[0, j]$ 的所有数字之和（包含 $0$ 和 $j$）。
- `preSum[i]`表示的是 `nums`数组中 $[0, i - 1]$ 的所有数字之和（包含 $0$ 和 $i - 1$）。
- 当两者相减时，结果留下了 `nums`数组中 $[i, j]$ 的所有数字之和。



例题 1.「力扣」的 [303. 区域和检索 - 数组不可变](https://leetcode-cn.com/problems/range-sum-query-immutable/)。

> 给定一个整数数组  `nums`，求出数组从索引 `i` 到 `j`（`i ≤ j`）范围内元素的总和，包含 `i`、`j` 两点。
>
> 实现 `NumArray` 类：
>
> - `NumArray(int[] nums)` 使用数组 `nums` 初始化对象
> - `int sumRange(int i, int j)` 返回数组 `nums` 从索引 `i` 到 `j`（`i ≤ j`）范围内元素的总和，包含 `i`、`j` 两点（也就是 `sum(nums[i], nums[i + 1], ... , nums[j])`）
>
>
> 示例：
>
> 输入：
> 	["NumArray", "sumRange", "sumRange", "sumRange"]
> 	[[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]
> 输出：
> 	[null, 1, -1, -3]
>
> 解释：
> 	NumArray numArray = new NumArray([-2, 0, 3, -5, 2, -1]);
> 	numArray.sumRange(0, 2); // return 1 ((-2) + 0 + 3)
> 	numArray.sumRange(2, 5); // return -1 (3 + (-5) + 2 + (-1)) 
> 	numArray.sumRange(0, 5); // return -3 ((-2) + 0 + 3 + (-5) + 2 + (-1))
>
>
> 提示：
>
> - `0 <= nums.length <= 104`
>
> - `-105 <= nums[i] <= 105`
> - `0 <= i <= j < nums.length`
> - 最多调用 $10^4$ 次 `sumRange` 方法

题意是给出了一个整数数组 `nums`，当调用 `sumRange(i, j)`函数的时候，求数组 `nums` 中 $i$ 到 $j$ 的所有元素总和（包含 $i$ 和 $j$）。

本题考察了利用「前缀和」计算数组的区间和，可以套用模板二。



<!-- tabs:start -->

#### **Java**

```java
class NumArray {
    private int[] preSum;
    public NumArray(int[] nums) {
        final int N = nums.length;
        preSum = new int[N + 1];
        for (int i = 0; i < N; ++i) {
            preSum[i + 1] = preSum[i] + nums[i];
        }
    }
    
    public int sumRange(int i, int j) {
        return preSum[j + 1] - preSum[i];
    }
}
```

#### **C++**

```c++
class NumArray {
public:
    NumArray(vector<int>& nums) {
        const int N = nums.size();
        preSum.resize(N + 1);
        for (int i = 0; i < N; ++i) {
            preSum[i + 1] = preSum[i] + nums[i];
        }
    }
    
    int sumRange(int i, int j) {
        return preSum[j + 1] - preSum[i];
    }
private:
    vector<int> preSum;
};
```

#### **Python**

```python
class NumArray:

    def __init__(self, nums: List[int]):
        N = len(nums)
        self.preSum = [0] * (N + 1)
        for i in range(N):
            self.preSum[i + 1] = self.preSum[i] + nums[i]

    def sumRange(self, i: int, j: int) -> int:
        return self.preSum[j + 1] - self.preSum[i]
```

<!-- tabs:end -->



**复杂度分析：**

- 空间复杂度：定义「前缀和」数组，需要 $N + 1$ 的空间，所以空间复杂度是 $O(N)$；
- 时间复杂度：
   - 初始化「前缀和」数组，需要把数组遍历一次，时间复杂度是 $O(N)$；
   - 求 $[i, j]$ 范围内的区间和，只用访问 `preSum[j + 1]` 和 `preSum[i]`，时间复杂度是 $O(1)$。