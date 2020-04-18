# 二分查找模板三（和模板二很像，但考虑细节更多）

说明：
+ 如果已经掌握了模板二，就无需掌握这个模板，可以简单看一下这个模板，对比模板二；
+ 这一版代码和模板二没有本质区别，一个显著的标志是：循环可以继续的条件是 `while (left + 1 < right)`，这说明在退出循环的时候，一定有 `left + 1 == right` 成立，也就是退出循环以后，区间有 2 个元素，即 `[left, right]`；
+ 这种写法的优点是：不用理解上一个版本在分支出现 `left = mid` 的时候中间数上取整的行为；
+ 缺点是显而易见的：
  + `while (left + 1 < right)` 写法相对于 `while (left < right)` 和 `while (left <= right)` 来说并不自然；
  + 由于退出循环以后，区间一定有两个元素，需要思考哪一个元素才是需要找的，即「后处理」一定要做，有些时候还会有先考虑 `left` 还是 `right` 的区别。

<!-- tabs:start -->

#### **Java**

```java
public int search(int[] nums, int left, int right, int target) {
    while (left + 1 < right) {
        // 选择中位数时下取整
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid;
        } else {
            right = mid;
        }
    }

    if (nums[left] == target) {
        return left;
    }
    if (nums[right] == target) {
        return right;
    }
    return -1;
}
```

#### **C++**

```cpp
int search(vector<int> &nums, int left, int right, int target) {
    while (left + 1 < right) {
        // 选择中位数时下取整
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid;
        } else {
            right = mid;
        }
    }

    if (nums[left] == target) {
        return left;
    }
    if (nums[right] == target) {
        return right;
    }
    return -1;
}
```

#### **Python3**

```python
def search(nums: List[int], left: int, right: int, target: int) -> int:
    while left + 1 < right:
        # 选择中位数时下取整
        mid = left + (right - left) // 2
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid
        else:
            right = mid

    if nums[left] == target:
        return left
    if nums[right] == target:
        return right
    return -1
```

<!-- tabs:end -->