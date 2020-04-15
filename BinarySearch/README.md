# 二分查找

作者：liweiwei1419（邮箱或者 github 地址） 审核：

二分查找是计算机科学中最基本、最有用的算法之一，在基础算法的学习中是非常重要的。

二分查找的最基本问题是在有序数组里查找一个特定的元素（「力扣」第 704 题：二分查找）。

### 二分查找的应用

二分查找法还可以应用在：

1、在半有序（旋转有序或者是山脉）数组里查找元素；

2、确定一个有范围的整数；

3、当需要查找的某个数值满足某个特点的性质。

### 二分查找模板一

「力扣」第 704 题：[二分查找](https://leetcode-cn.com/problems/binary-search/)。

> 给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target`，写一个函数搜索 `nums` 中的 `target`，如果目标值存在返回下标，否则返回 `-1`。

思路：

+ 在一个有序数组里查找元素，特别像以前电视「猜价格」上的猜价格游戏：运气好，一下子猜中，如果主持人说猜高了，下一步就应该往低了猜，如果主持人说猜低了，下一步就应该就往高了猜；

我们把待搜索区间的左边界下标设置为 `left`，右边界下标设置为 ` right` 。

+ 这个思路把待搜索区间 `[left, right]` 分为 3 个部分：
  +  `mid` 位置（只有 1 个元素）；
  + `[left, mid - 1]` 里的所有元素；
  + `[mid + 1, right]` 里的所有元素；


于是，二分查找就是不断地在区间 `[left, right]` 里根据 `left` 和 `right` 的中间位置 `mid = (left + right) / 2` 的元素大小，也就是看 `nums[mid]` 与 `target` 的大小关系：
+ 如果 `nums[mid] == target` ，返回 `mid`；
+ 如果 `nums[mid] > target` ，由于数组有序，`mid` 以及 `mid` 右边的所有元素都大于 `target`，目标元素一定在区间 `[left, mid - 1]` 里，因此设置 `right = mid - 1`；
+ 如果 `nums[mid] < target` ，由于数组有序，`mid` 以及 `mid` 左边的所有元素都小于 `target`，目标元素一定在区间 `[mid + 1, right]` 里，因此设置 `left = mid + 1`。

Java 代码：

```java
class Solution {

    public int search(int[] nums, int target) {
        // 特殊用例判断
        int len = nums.length;
        if (len == 0) {
            return -1;
        }
        // 在 [left, right] 区间里查找 target
        int left = 0;
        int right = len - 1;
        while (left <= right) {
            // 为了防止 left + right 整形溢出，写成如下性质
            int mid = left + (right - left) / 2;

            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] > target) {
                // 下一轮搜索区间：[left, mid - 1]
                right = mid - 1;
            } else {
                // 此时：nums[mid] < target
                // 下一轮搜索区间：[mid + 1, right]
                left = mid + 1;
            }
        }
        return -1;
    }
}
```

注意事项：

+ 许多刚刚写的朋友，经常在写  `left = mid + 1;` 还是写 `right = mid - 1;` 上感到困惑，一个行之有效的思考策略是：**永远去想下一轮目标元素应该在哪个区间里：**
  + 如果目标元素在区间 `[left, mid - 1]` 里，就需要设置设置 `right = mid - 1`； 
  + 如果目标元素在区间 `[mid + 1, right]` 里，就需要设置设置 `left = mid + 1`； 

考虑不仔细是初学二分法容易出错的地方，这一步切忌跳步。

+ 二分查找算法是典型的「减治思想」的应用，我们使用二分查找将待搜索的区间逐渐缩小，以达到「缩减问题规模」的目的；
+ 循环可以继续的条件是 `while (left <= right)`，特别地，当 `left == right` 即当待搜索区间里只有一个元素的时候，查找也必须进行下去；
+ `int mid = (left + right) / 2;` 在 `left + right` 整形溢出的时候，`mid` 会变成负数，回避这个问题的办法是写成 `int mid = left + (right - left) / 2;`。
