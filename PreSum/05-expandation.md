

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
> ![](04-2d-matrix.png)
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

![](04-2d-matrix-step1.png)
减去 $S(O, A)$ 的原因是 $S(O, C)$ 和 $S(O, B)$ 中都有 $S(O, A)$，即加了两次 $S(O, A)$，所以需要减去一次 $S(O, A)$。


### 步骤二：根据 preSum 求子矩形面积
前面已经求出了数组中从 `[0,0]` 位置到 `[i,j]` 位置的 `preSum`。


可以用 `preSum` 计算 `[row1, col1]` 到 `[row2, col2]` 的子矩形的所有元素之和：


$preSum[row2][col2] - preSum[row2][col1 - 1] - preSum[row1 - 1][col2] + preSum[row1 - 1][col1 - 1]$ 


同样利用一张图来说明：


$S(A, D) = S(O, D) - S(O, E) - S(O, F) + S(O, G)$

![](04-2d-matrix-step2.png)

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

