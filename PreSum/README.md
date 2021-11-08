# 前缀和

作者：fuxuemingzhu；审核：liweiwei1419。

前缀和（`preSum`）算法是一种数据预处理方法，可用于快速求数组的区间和。前缀和是一种典型的空间换时间思想的应用。

前缀和可以简单地理解为数组的前 i 个元素的和。

## 前缀和的应用

前缀和可以应用在：

1. 快速求数组前 $i$ 位之和；
2. 快速求数组的 $[i, j]$ 范围内之和；
3. 求二维矩阵中某个子矩阵之和。

## 模板

本文提供两个模板：

1. 模板一：基础模板，这个模板定义的 `preSum` 数组长度等于 $nums$ 数组长度。
2. 模板二：常用模板，这个模板定义的 `preSum` 数组长度等于 $nums$ 数组长度 $+ 1$。

**在刷题的时候，常见的写法是模板二**。建议先阅读模板一，了解前缀和原理，然后再看模板二。实际使用的时候，两个模板并没有本质区别，选择自己喜欢的即可。

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

>「前缀和」 就是从 `nums` 数组中的第  0 位置开始，累加到第 $i$ 位置的结果，我们常把这个结果保存到数组 `preSum` 中，记为  `preSum[i]`。

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

![](https://picture-bed-1251805293.cos.ap-beijing.myqcloud.com/202111082247909.png)

至此，我们已经把如何求「前缀和」以及如何用「前缀和」求数组的区间和讲解清楚了。

# 前缀和模板二

模板一中，定义的 `preSum[i] = nums[0] + nums[1] + ...+ nums[i]` ，那么区间 $[i, j]$ 内的元素之和为 ：

> $sum(i, j) = preSum[j] - preSum[i - 1]$

当要计算区间 $[0, j]$ 内的元素之和时，由于 $i = 0$，所以 `preSum[i - 1]` 会越界，因此需要对以 $0$ 为开始的前缀和进行分类讨论。

为了避免分类讨论，实际在使用前缀和时，**经常把前缀和的数组长度定义为数组长度 + 1。**

即令 `preSum[0] = 0`  ，

> $$preSum[i] = nums[0] + nums[1] + ... + nums[i - 1]$$。

那么，就可以把 `preSum` 的公式统一为 **`preSum[i] = preSum[i - 1] + nums[i - 1]`，**此时的 **`preSum[i]`** 表示  **`nums`** 中 $i$ 元素左边所有元素之和（不包含当前元素 $i$）。

下面以 `[1, 12, -5, -6, 50, 3]` 为例，用动图讲解一下如何求 `preSum`。

![02-preSum](https://picture-bed-1251805293.cos.ap-beijing.myqcloud.com/202111082248432.gif)

求 `preSum` 数组的过程是——

```c++
preSum[0] = 0;
preSum[1] = preSum[0] + nums[0];
preSum[2] = preSum[1] + nums[1];
...
```



那么区间 $[i, j]$ 内元素之和的计算公式为： 

> $sum(i, j) = preSum[j + 1] - preSum[i]$

什么原理呢？其实就是消除公共部分即 $[0, i-1]$ 部分的和，那么就能得到 $[i, j]$ 部分的区间和。


注意上面的式子中，使用的是 `preSum[j + 1]` 和 `preSum[i]`，原因是：

- `preSum[j + 1]` 表示的是 `nums` 数组中 $[0, j]$ 的所有数字之和（包含 $0$ 和 $j$）。
- `preSum[i]`表示的是 `nums`数组中 $[0, i - 1]$ 的所有数字之和（包含 $0$ 和 $i - 1$）。
- 当两者相减时，结果留下了 `nums`数组中 $[i, j]$ 的所有数字之和。



求前缀和的代码如下：



<!-- tabs:start -->

#### **Java**

```java
class Solution {
    public int[] runningSum(int[] nums) {
        int N = nums.length;
        int[] preSum = new int[N + 1];
        for (int i = 0; i < N; ++i) {
            preSum[i + 1] = preSum[i] + nums[i];
        }
        return preSum;
    }
}
```

#### **C++**


```c++
vector<int> runningSum(vector<int>& nums) {
    const int N = nums.size();
    vector<int> preSum(N + 1, 0);
    for (int i = 0; i < N; ++i) {
        preSum[i + 1] = preSum[i] + nums[i]; 
    }
    return preSum;
}
```

#### **Python**

```python
class Solution(object):
    def runningSum(self, nums):
        N = len(nums)
        preSum = [0] * (N + 1)
        for i in range(0, N):
            preSum[i + 1] = preSum[i] + nums[i]
        return preSum
```

<!-- tabs:end -->

注意，上面的代码中没有给 `preSum[0]` 赋值，在 C++ 中 `vector<int>` 的默认值为 0。

- 时间复杂度：$O(N)$;

- 空间复杂度：$O(N)$。





「前缀和」的思想比较简单，分为两个步骤，需要注意细节：

1. 预处理得到前缀和 `preSum` 数组（这一步很少出问题）；
2. 通过 `preSum` 数组计算数组中某个区间的和（这一步可能出问题，需要注意定义的 `preSum[i]` 是否包含了$nums[i]$）。

刷题时，难点在于怎么想到使用「前缀和」—— 如果题目考察了「区间和」，那么可以考虑「前缀和」；另外也可以考虑用滑动窗口。



# 前缀和的应用

## 求数组的区间和

利用 `preSum` 数组，可以在 $O(1)$ 的时间内快速求出 `nums`  任意区间 $[i, j]$ (两端都包含) 内的所有元素之和。

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





# 前缀和拓展

## 拓展一：「前缀和」与「哈希表」

例题 1.「力扣」的 [560. 和为 K 的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)。

> 给你一个整数数组 nums 和一个整数 k ，请你统计并返回该数组中和为 k 的连续子数组的个数。
>
> 示例 1：
>
> 输入：nums = [1,1,1], k = 2
> 输出：2
>
> 示例 2：
>
> 输入：nums = [1,2,3], k = 3
> 输出：2
>
>
> 提示：
>
> - 1 <= nums.length <= 2 * 10^4
> - -1000 <= nums[i] <= 1000
> - -10^7 <= k <= 10^7

如果本题不使用「前缀和」，那么需要使用三重循环：两重循环用于遍历所有区间，一重循环用于区间求和。时间复杂度是 $O(N^3)$。

其中，区间求和的循环可以用「前缀和」代替，把整体的时间复杂度降低为 $O(N^2)$。

还可以进一步优化：类似于「两数之和」的做法，我们可以用一个字典（哈希表）保存已经遇到过的 `preSum` 的出现次数，那么对于一个位置 $i$ ，可以在 $O(1)$ 的时间内，找到有多少个以 $i$ 结尾的子数组之和为 $k$。从而把时间复杂度降低为 $O(N)$。

对于遍历刚开始的时候，下标 $0$ 之前没有元素，需要设置字典（哈希表）中前缀和 $0$ 的出现的次数为 $1$，即 `visited[0] = 1;` 以保证当 $[0, i]$ 的区间和为 $k$ 时，累计上该区间的次数为 1。

<!-- tabs:start -->

#### **Java**

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int preSum = 0;
        Map<Integer, Integer> visited = new HashMap<>();
        visited.put(0, 1);
        int res = 0;
        for (int i = 0; i < nums.length; ++i) {
            preSum += nums[i];
            if (visited.containsKey(preSum - k)) {
                res += visited.get(preSum - k);
            }
            visited.put(preSum, visited.getOrDefault(preSum, 0) + 1);
        }
        return res;
    }
}
```



####**C++**

```c++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int preSum = 0;
        unordered_map<int, int> visited;
        visited[0] = 1;
        int res = 0;
        for (int num : nums) {
            preSum += num;
            if (visited.count(preSum - k)) {
                res += visited[preSum - k];
            }
            visited[preSum] ++;
        }
        return res;
    }
};
```

#### **Python**

```python
class Solution(object):
    def subarraySum(self, nums, k):
        preSum = 0
        visited = collections.defaultdict(int)
        visited[0] = 1
        res = 0
        for num in nums:
            preSum += num
            if preSum - k in visited:
                res += visited[preSum - k]
            visited[preSum] += 1
        return res
```

<!-- tabs:end -->

## 拓展二：二维矩阵的「前缀和」

另外一种拓展，是求二维矩阵的「前缀和」。

例题 2. 「力扣」的 [304. 二维区域和检索 - 矩阵不可变](https://leetcode-cn.com/problems/range-sum-query-2d-immutable/)。

> 给定一个二维矩阵 `matrix`，以下类型的多个请求：
>
> 计算其子矩形范围内元素的总和，该子矩阵的 **左上角** 为 `(row1, col1)`，右下角 为 `(row2, col2)` 。
> 实现 `NumMatrix` 类：
>
> - `NumMatrix(int[][] matrix)` 给定整数矩阵 `matrix` 进行初始化
> - `int sumRegion(int row1, int col1, int row2, int col2)` 返回 左上角 `(row1, col1)` 、右下角 `(row2, col2)` 所描述的子矩阵的元素 总和 。
>
>
> 示例 1：
>
> ![](https://picture-bed-1251805293.cos.ap-beijing.myqcloud.com/202111082248993.png)
>
> 输入: 
>
> ​	["NumMatrix","sumRegion","sumRegion","sumRegion"]
>
> ​	[[[[3,0,1,4,2],[5,6,3,2,1],[1,2,0,1,5],[4,1,0,1,7],[1,0,3,0,5]]],[2,1,4,3],[1,1,2,2],[1,2,2,4]]
>
> 输出: 
>
> ​	[null, 8, 11, 12]
>
> 
>
> 解释:
>
> ​	NumMatrix numMatrix = new NumMatrix([[3,0,1,4,2],[5,6,3,2,1],[1,2,0,1,5],[4,1,0,1,7],[1,0,3,0,5]]]);
> ​	
>
> ​	numMatrix.sumRegion(2, 1, 4, 3); // return 8 (红色矩形框的元素总和)
> ​	
>
> ​	numMatrix.sumRegion(1, 1, 2, 2); // return 11 (绿色矩形框的元素总和)
> ​	
>
> ​	numMatrix.sumRegion(1, 2, 2, 4); // return 12 (蓝色矩形框的元素总和)

当「前缀和」拓展到二维区间时，可以用下面的思路求解。

### 步骤一：求 preSum


我们定义 `preSum[i][j]` 表示 从 `[0,0]` 位置到 `[i,j]` 位置的子矩形所有元素之和。


如果求 `preSum[i][j]` 的递推公式为：


$preSum[i][j] = preSum[i - 1][j] + preSum[i][j - 1] - preSum[i - 1][j - 1] + matrix[i][j]$


可以用下图帮助理解：


$S(O, D) = S(O, C) + S(O, B) - S(O, A) + D$

![](https://picture-bed-1251805293.cos.ap-beijing.myqcloud.com/202111082248010.png)
减去 $S(O, A)$ 的原因是 $S(O, C)$ 和 $S(O, B)$ 中都有 $S(O, A)$，即加了两次 $S(O, A)$，所以需要减去一次 $S(O, A)$。


### 步骤二：根据 preSum 求子矩形面积

前面已经求出了数组中从 `[0,0]` 位置到 `[i,j]` 位置的 `preSum`。


可以用 `preSum` 计算 `[row1, col1]` 到 `[row2, col2]` 的子矩形的所有元素之和：


$preSum[row2][col2] - preSum[row2][col1 - 1] - preSum[row1 - 1][col2] + preSum[row1 - 1][col1 - 1]$ 


同样利用一张图来说明：


$S(A, D) = S(O, D) - S(O, E) - S(O, F) + S(O, G)$

![](https://picture-bed-1251805293.cos.ap-beijing.myqcloud.com/202111082248023.png)

加上子矩形 $S(O, G)$ 面积的原因是 $S(O, E)$ 和 $S(O, F)$ 中都有 $S(O, G)$，即减了两次 $S(O, G)$，所以需要加上一次 $S(O, G)$。



代码如下。

<!-- tabs:start -->

#### **Java**

```java
class NumMatrix {
    int[][] preSum;

    public NumMatrix(int[][] matrix) {
        int M = matrix.length;
        if (M > 0) {
            int N = matrix[0].length;
            preSum = new int[M + 1][N + 1];
            for (int i = 0; i < M; ++i) {
                for (int j = 0; j < N; ++j) {
                    preSum[i + 1][j + 1] = preSum[i][j + 1] + preSum[i + 1][j] - preSum[i][j] + matrix[i][j];
                }
            }
        }
    }
    
    public int sumRegion(int row1, int col1, int row2, int col2) {
        return preSum[row2 + 1][col2 + 1] - preSum[row2 + 1][col1] - preSum[row1][col2 + 1] + preSum[row1][col1]; 
    }
}
```

#### **C++**

```C++
class NumMatrix {
private:
    vector<vector<int>> preSum;
public:
    NumMatrix(vector<vector<int>>& matrix) {
        const int M = matrix.size();
        if (M > 0) {
            const int N = matrix[0].size();
            preSum.resize(M + 1, vector<int>(N + 1));
            for (int i = 0; i < M; ++i) {
                for (int j = 0; j < N; ++j) {
                    preSum[i + 1][j + 1] = preSum[i + 1][j] + preSum[i][j + 1] - preSum[i][j] + matrix[i][j];
                }
            }
        }
    }
    
    int sumRegion(int row1, int col1, int row2, int col2) {
        return preSum[row2 + 1][col2 + 1] - preSum[row2 + 1][col1] - preSum[row1][col2 + 1] + preSum[row1][col1];
    }
};
```

#### **Python**

```python
class NumMatrix:

    def __init__(self, matrix: List[List[int]]):
        if not matrix or not matrix[0]:
            M, N = 0, 0
        else:
            M, N = len(matrix), len(matrix[0])
        self.preSum = [[0] * (N + 1) for _ in range(M + 1)]
        for i in range(M):
            for j in range(N):
                self.preSum[i + 1][j + 1] = self.preSum[i][j + 1] + self.preSum[i + 1][j]  - self.preSum[i][j] + matrix[i][j]


    def sumRegion(self, row1: int, col1: int, row2: int, col2: int) -> int:
        return self.preSum[row2 + 1][col2 + 1] - self.preSum[row2 + 1][col1] - self.preSum[row1][col2 + 1] + self.preSum[row1][col1]
```

<!-- tabs:end -->

**复杂度分析：**

- 空间复杂度：定义二维的「前缀和」数组，需要 $(M + 1) * (N + 1)$ 的空间，所以空间复杂度是 $O(M*N)$；
- 时间复杂度：
  - 初始化「前缀和」数组，需要把二维数组遍历一次，时间复杂度是 $O(M*N)$；
  - 求  `[row1, col1]` 到 `[row2, col2]` 的子矩形的所有元素之和，只用访问 `preSum` 中的四个元素，时间复杂度是 $O(1)$。

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

