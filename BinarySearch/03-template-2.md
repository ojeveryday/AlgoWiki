# 二分查找模板二（推荐）

这个版本的模板推荐使用的原因是：需要考虑的细节最少，编码时不容易出错。

版本 1：

<!-- tabs:start -->

#### **Java**

```java
public int search(int[] nums, int left, int right, int target) {
    while (left < right) {
        // 选择中位数时下取整
        int mid = left + (right - left) / 2;
        if (check(mid)) {
            // 下一轮搜索区间是 [mid + 1, right]
            left = mid + 1
        } else {
            // 下一轮搜索区间是 [left, mid]
            right = mid
        }
    }
    // 退出循环的时候，程序只剩下一个元素没有看到。
    // 视情况，是否需要单独判断 left（或者 right）这个下标的元素是否符合题意
}
```

#### **C++**

```cpp
int search(vector<int> &nums, int left, int right, int target) {
    while (left < right) {
        // 选择中位数时下取整
        int mid = left + (right - left) / 2;
        if (check(mid)) {
            // 下一轮搜索区间是 [mid + 1, right]
            left = mid + 1;
        } else {
            // 下一轮搜索区间是 [left, mid]
            right = mid;
        }
    }
    // 退出循环的时候，程序只剩下一个元素没有看到。
    // 视情况，是否需要单独判断 left（或者 right）这个下标的元素是否符合题意
}
```

#### **Python3**

```python
def search(nums: List[int], left: int, right: int, target: int) -> int:
    while left < right:
        # 选择中位数时下取整
        mid = left + (right - left) // 2
        if check(mid):
            # 下一轮搜索区间是 [mid + 1, right]
            left = mid + 1
        else:
            # 下一轮搜索区间是 [left, mid]
            right = mid
    # 退出循环的时候，程序只剩下一个元素没有看到。
    # 视情况，是否需要单独判断 left（或者 right）这个下标的元素是否符合题意
```

<!-- tabs:end -->

版本 2：

<!-- tabs:start -->

#### **Java**

```java
public int search(int[] nums, int left, int right, int target) {
    while (left < right) {
        // 选择中位数时上取整
        int mid = left + (right - left + 1) / 2;
        if (check(mid)) {
            // 下一轮搜索区间是 [left, mid - 1]
            right = mid - 1;
        } else {
            // 下一轮搜索区间是 [mid, right]
            left = mid;
        }
    }
    // 退出循环的时候，程序只剩下一个元素没有看到。
    // 视情况，是否需要单独判断 left（或者 right）这个下标的元素是否符合题意
}
```

#### **C++**

```cpp
int search(vector<int> &nums, int left, int right, int target) {
    while (left < right) {
        // 选择中位数时上取整
        int mid = left + (right - left + 1) / 2;
        if (check(mid)) {
            // 下一轮搜索区间是 [left, mid - 1]
            right = mid - 1;
        } else {
            // 下一轮搜索区间是 [mid, right]
            left = mid;
        }
    }
    // 退出循环的时候，程序只剩下一个元素没有看到。
    // 视情况，是否需要单独判断 left（或者 right）这个下标的元素是否符合题意
}
```

#### **Python3**

```python
def search(nums: List[int], left: int, right: int, target: int) -> int:
    while left < right:
        # 选择中位数时上取整
        mid = left + (right - left + 1) // 2
        if check(mid):
            # 下一轮搜索区间是 [left, mid - 1]
            right = mid - 1
        else:
            # 下一轮搜索区间是 [mid, right]
            left = mid
    # 退出循环的时候，程序只剩下一个元素没有看到。
    # 视情况，是否需要单独判断 left（或者 right）这个下标的元素是否符合题意
```

<!-- tabs:end -->

理解模板代码的要点：

+ 核心思想：虽然模板有两个，但是核心思想只有一个，那就是：把待搜索的目标元素放在最后判断，每一次循环排除掉不存在目标元素的区间，目的依然是确定下一轮搜索的区间；
+ 特征：`while (left < right)`，这里使用严格小于 `<` 表示的临界条件是：当区间里的元素只有 2 个时，依然可以执行循环体。换句话说，退出循环的时候一定有 `left == right` 成立，**这一点在定位元素下标的时候极其有用**；
+ 在循环体中，先考虑 `nums[mid]` 在满足什么条件下不是目标元素，进而考虑两个区间 `[left, mid - 1]` 以及 `[mid + 1, right]` 里元素的性质，目的依然是确定下一轮搜索的区间；
**注意 1**：先考虑什么时候不是解，是一个经验，在绝大多数情况下不易出错，重点还是确定下一轮搜索的区间，由于这一步不容易出错，它的反面（也就是 `else` 语句的部分），就不用去考虑对应的区间是什么，直接从上一个分支的反面区间得到，进而确定边界如何设置；
+ 根据边界情况，看取中间数的时候是否需要上取整；
**注意 2**： 这一步也依然是根据经验，建议先不要记住结论，在使用这个思想解决问题的过程中，去思考可能产生死循环的原因，进而理解什么时候需要在括号里加 1 ，什么时候不需要；
+ 在退出循环以后，根据情况看是否需要对下标为 `left` 或者 `right` 的元素进行单独判断，这一步叫「后处理」。在有些问题中，排除掉所有不符合要求的元素以后，剩下的那 1 个元素就一定是目标元素。如果根据问题的场景，目标元素一定在搜索区间里，那么退出循环以后，可以直接返回 `left`（或者 `right`）。

以上是这两个模板写法的所有要点，并且是高度概括的。请读者一定先抓住这个模板的核心思想，在具体使用的过程中，不断地去体会这个模板使用的细节和好处。只要把中间最难理解的部分吃透，几乎所有的二分问题就都可以使用这个模板来解决，因为「减治思想」是通用的。好处在这一小节的开篇介绍过了，需要考虑的细节最少。

学习建议：一定需要多做练习，体会这（两）个模板的使用。

注意事项：

+ 先写分支，再决定中间数是否上取整；
+ 在使用多了以后，就很容易记住，只要看到 `left = mid` ，它对应的取中位数的取法一定是 `int mid = left + (right - left + 1) / 2;`。