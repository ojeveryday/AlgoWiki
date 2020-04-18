# 精选例题

## 题型 1：在有序数组里查找 lower_bound 和 upper_bound

+ lower_bound：查找第一个大于或等于 target 的数字；
+ upper_bound：查找第一个大于 target 的数字。

这一类问题的描述经常让人觉得头晕，使用模板一，就需要考虑返回 `left` 还是 `right`。

如果使用模板二，由于退出循环以后一定有 `left == right`，就只需要单独判断 `left` 是否满足题意。

例题 2：「力扣」第 35 题：[搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)。

> 给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。


分析：

+ 根据题意，要我们找的是第 1 个大于或者等于 `target` 的元素的下标，因此小于 `target` 的元素一定不是解。根据这一点，使用模板二完成编码；
+ 由于插入元素的位置一定存在，这里无需后处理，但前面搜索范围的区间需要做特殊判断。

请读者比较下面两版代码的区别：

<!-- tabs:start -->

#### **Java**

```java
public class Solution {

    public int searchInsert(int[] nums, int target) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }

        // 特判
        if (nums[len - 1] < target) {
            return len;
        }
        int left = 0;
        int right = len - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            // 严格小于 target 的元素一定不是解
            if (nums[mid] < target) {
                // 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
}
```

#### **C++**

```cpp
class Solution {
   public:
    int searchInsert(vector<int> &nums, int target) {
        int len = nums.size();
        if (len == 0) {
            return 0;
        }

        // 特判
        if (nums[len - 1] < target) {
            return len;
        }
        int left = 0;
        int right = len - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            // 严格小于 target 的元素一定不是解
            if (nums[mid] < target) {
                // 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
};
```

#### **Python3**

```python
class Solution(object):

    def searchInsert(self, nums: List[int], target: int) -> int:
        n = len(nums)
        if n == 0:
            return 0

        # 特判
        if nums[n - 1] < target:
            return n

        left, right = 0, n - 1
        while left < right:
            mid = left + (right - left) // 2
            # 严格小于 target 的元素一定不是解
            if nums[mid] < target:
                # 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1
            else:
                right = mid
        return left

```

<!-- tabs:end -->

<!-- tabs:start -->

#### **Java**

```java
public class Solution {

    public int searchInsert(int[] nums, int target) {
        int len = nums.length;
        if (len == 0) {
            return 0;
        }

        int left = 0;
        // 因为有可能数组的最后一个元素的位置的下一个是我们要找的，故右边界是 len
        int right = len;

        while (left < right) {
            int mid = left + (right - left) / 2;
            // 严格小于 target 的元素一定不是解
            if (nums[mid] < target) {
                // 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
}
```

#### **C++**

```cpp
class Solution {
   public:
    int searchInsert(vector<int> &nums, int target) {
        int len = nums.size();
        if (len == 0) {
            return 0;
        }

        int left = 0;
        // 因为有可能数组的最后一个元素的位置的下一个是我们要找的，故右边界是 len
        int right = len;

        while (left < right) {
            int mid = left + (right - left) / 2;
            // 严格小于 target 的元素一定不是解
            if (nums[mid] < target) {
                // 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
};
```

#### **Python3**

```python
class Solution(object):

    def searchInsert(self, nums: List[int], target: int) -> int:
        n = len(nums)
        if n == 0:
            return 0

        # 因为有可能数组的最后一个元素的位置的下一个是我们要找的，故右边界是 len
        left, right = 0, n

        while left < right:
            mid = left + (right - left) // 2
            # 严格小于 target 的元素一定不是解
            if nums[mid] < target:
                # 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1
            else:
                right = mid
        return left
```

<!-- tabs:end -->

**复杂度分析**：

+ 时间复杂度：$O(\log N)$，这里 $N$ 是数组的长度，每一次都将问题的规模缩减为原来的一半，因此时间复杂度是对数级别的；
+ 空间复杂度：$O(1)$。


## 题型 2：确定一个有范围的整数

例题 3：「力扣」第 69 题：[x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

> 实现 `int sqrt(int x)` 函数。

计算并返回 `x` 的平方根，其中 `x` 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

分析：

+ 由于题目要求我们求的是一个四舍五入的整数，并且一个数的平方根肯定不会超过它自己，因此可以使用二分查找法定位这个整数；
+ 直觉还告诉我们，一个数的平方根最多不会超过它的一半，例如 $8$ 的平方根，$8$ 的一半是 $4$，$4^2=16>8$，如果这个数越大越是如此，因此我们要计算一下，这个边界是多少。为此，解如下不等式：

$$\left(\cfrac{a}{2}\right)^2 \ge a$$

意即：如果一个数的一半的平方大于它自己，那么这个数的取值范围。解以上不等式得 $a \ge 4$ 或者 $a \le 0$。

于是边界值就是 $4$，那么对 $0$、$1$、$2$、$3$ 分别计算结果，很容易知道，这 $4$ 个数的平方根依次是 $0$、$1$、$1$、$1$。

**注意**：这 $4$ 个特值如果没有考虑到，有可能导致你设置的搜索边界不正确。在使用二分法寻找平方根的时候，要特别注意边界值的选择。

<!-- tabs:start -->

#### **Java**

```java
public class Solution {

    public int mySqrt(int x) {
        if (x == 0) {
            return 0;
        }
        if (x == 1) {
            return 1;
        }

        int left = 1;
        int right = x / 2;
        while (left < right) {
            int mid = left + (right - left + 1) / 2;
            // 不使用 mid * mid > x，防止 overflow
            if (mid > x / mid) {
                // 下一轮搜索的区间是 [left, mid - 1]
                right = mid - 1;
            } else {
                // 下一轮搜索的区间是 [mid, right]
                left = mid;
            }
        }
        return left;
    }
}
```

#### **C++**

```cpp
class Solution {
   public:
    int mySqrt(int x) {
        if (x == 0) {
            return 0;
        }
        if (x == 1) {
            return 1;
        }

        int left = 1;
        int right = x / 2;
        while (left < right) {
            int mid = left + (right - left + 1) / 2;
            // 不使用 mid * mid > x，防止 overflow
            if (mid > x / mid) {
                // 下一轮搜索的区间是 [left, mid - 1]
                right = mid - 1;
            } else {
                // 下一轮搜索的区间是 [mid, right]
                left = mid;
            }
        }
        return left;
    }
};
```

#### **Python3**

```python
class Solution(object):

    def mySqrt(self, x: int) -> int:
        if x == 0:
            return 0
        if x == 1:
            return 1

        left, right = 1, x // 2
        while left < right:
            mid = left + (right - left + 1) // 2
            # 不使用 mid * mid > x，防止 overflow
            # Python 使用 BigInteger，所以不用担心溢出，但还是推荐使用如下形式
            if mid > x // mid:
                # 下一轮搜索的区间是 [left, mid - 1]
                right = mid - 1
            else:
                # 下一轮搜索的区间是 [mid, right]
                left = mid
        return left
```

<!-- tabs:end -->

注意：这里看到分支的设置为 `left = mid;` 一定要在 `int mid = left + (right - left) / 2;` 的括号里加 `1`，得：`int mid = left + (right - left + 1) / 2;`。

**复杂度分析：**

+ 时间复杂度：$O(\log N)$，二分法的时间复杂度是对数级别的；
+ 空间复杂度：$O(1)$，使用了常数个数的辅助空间用于存储和比较。

## 题型 3：需要查找的目标元素满足某个特定的性质

说明：这一类问题判别条件不是一个表达式，很多时候需要抽取成一个函数。

例题 4：「力扣」第 875 题：[爱吃香蕉的珂珂](https://leetcode-cn.com/problems/koko-eating-bananas/)

珂珂喜欢吃香蕉。这里有 `N` 堆香蕉，第 `i` 堆中有 `piles[i]` 根香蕉。警卫已经离开了，将在 `H` 小时后回来。

珂珂可以决定她吃香蕉的速度 `K` （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 `K` 根。如果这堆香蕉少于 `K` 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。

珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。

返回她可以在 `H` 小时内吃掉所有香蕉的最小速度 `K`（`K` 为整数）。

示例 1：

```
输入: piles = [3,6,7,11], H = 8
输出: 4
```


示例 2：

```
输入: piles = [30,11,23,4,20], H = 5
输出: 30
```


示例 3：

```
输入: piles = [30,11,23,4,20], H = 6
输出: 23
```


分析：

+ 由于速度是一个有范围的整数，因此可以使用二分查找法解决这个问题。而确定速度不是一个表达式能完成的，需要封装成一个函数；
+ 速度越小，耗时越多；
+ 搜索的是速度。因为题目限制了珂珂一个小时之内只能选择一堆香蕉吃，因此速度最大值就是这几堆香蕉中，数量最多的那一堆。速度的最小值是 1（其实还可以再分析一下下界是多少）；
+ 还是因为珂珂一个小时之内只能选择一堆香蕉吃，因此：**每堆香蕉吃完的耗时 = 这堆香蕉的数量 / 珂珂一小时吃香蕉的数量**，这里的 `/` 在不能整除的时候，需要上取整。

<!-- tabs:start -->

#### **Java**

```java
public class Solution {

    public int minEatingSpeed(int[] piles, int H) {
        int maxVal = 1;
        for (int pile : piles) {
            maxVal = Math.max(maxVal, pile);
        }

        // 速度最小的时候，耗时最长
        int left = 1;
        // 速度最大的时候，耗时最短
        int right = maxVal;

        while (left < right) {
            int mid = left + (right - left) / 2;

            if (calculateSum(piles, mid) > H) {
                // 耗时太多，说明速度太慢了，下一轮搜索区间在
                // [mid + 1, right]
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }

    /**
     * 如果返回的小时数严格大于 H，就不符合题意
     *
     * @param piles
     * @param speed
     * @return 需要的小时数
     */
    private int calculateSum(int[] piles, int speed) {
        int sum = 0;
        for (int pile : piles) {
            // 上取整可以这样写
            sum += (pile + speed - 1) / speed;

        }
        return sum;
    }
}
```

#### **C++**

```cpp
class Solution {
   public:
    int minEatingSpeed(vector<int> &piles, int H) {
        int maxVal = 1;
        for (int pile : piles) {
            maxVal = max(maxVal, pile);
        }

        // 速度最小的时候，耗时最长
        int left = 1;
        // 速度最大的时候，耗时最短
        int right = maxVal;

        while (left < right) {
            int mid = left + (right - left) / 2;

            if (calculateSum(piles, mid) > H) {
                // 耗时太多，说明速度太慢了，下一轮搜索区间在
                // [mid + 1, right]
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }

    /**
     * 如果返回的小时数严格大于 H，就不符合题意
     *
     * @param piles
     * @param speed
     * @return 需要的小时数
     */
   private:
    int calculateSum(vector<int> &piles, int speed) {
        int sum = 0;
        for (int pile : piles) {
            // 上取整可以这样写
            sum += (pile + speed - 1) / speed;
        }
        return sum;
    }
};
```

#### **Python3**

```python
class Solution:

    def minEatingSpeed(self, piles: List[int], H: int) -> int:
        maxVal = 1
        for pile in piles:
            maxVal = max(maxVal, pile)

        # 速度最小的时候，耗时最长
        left = 1
        # 速度最大的时候，耗时最短
        right = maxVal

        while left < right:
            mid = left + (right - left) // 2

            if self.calculateSum(piles, mid) > H:
                # 耗时太多，说明速度太慢了，下一轮搜索区间在
                # [mid + 1, right]
                left = mid + 1
            else:
                right = mid
        return left

    def calculateSum(self, piles: List[int], speed: int) -> int:
        """如果返回的小时数严格大于 H，就不符合题意

        Args:
            piles:
            speed:

        Returns:
            需要的小时数
        """
        sum = 0
        for pile in piles:
            # 上取整可以这样写
            sum += (pile + speed - 1) // speed
        return sum

```

<!-- tabs:end -->