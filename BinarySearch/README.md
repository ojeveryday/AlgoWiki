# 二分查找算法

作者：liweiwei1419；OneDirection9；审核：liweiwei1419；zerotrac。

二分查找是计算机科学中最基本、最有用的算法之一，在基础算法的学习中是非常重要的。

二分查找的最基本问题是在有序数组里查找一个特定的元素（「力扣」第 704 题：二分查找）。

## 二分查找的应用

二分查找法还可以应用在：

1、在半有序（旋转有序或者是山脉）数组里查找元素；

2、确定一个有范围的整数；

3、需要查找的目标元素满足某个特定的性质。

## 学习建议

这里写成模板只是为了方便大家学习，但是学习算法更重要的是掌握思想，这需要通过大量的练习。希望大家能够在练习的过程中，不断体会二分查找法的「减治思想」。熟悉以后，这些模板都无需且不应该记忆，编码应该是十分自然的事情。

## 二分查找的三个模板

说明：这里提供了三个模板，它们的关系是这样的：

+ 模板一：最好理解的版本，但是在刷题的过程中，需要处理一些边界的问题，一不小心容易出错；
+ 模板二：强烈推荐掌握的版本，应先理解思想，再通过实际应用去体会这个模板的细节，熟练使用以后就会觉得非常自然；
+ 模板三：可以认为是模板二的避免踩坑版本，只要深刻理解了模板二，模板三就不在话下。

实际应用中，选择最好理解的版本即可。

这里有一个提示：模板二考虑的细节最少，可以用于解决一些相对复杂的问题。缺点是：学习成本较高，初学的时候比较容易陷入死循环，建议大家通过多多使用，并且尝试 debug，找到死循环的原因，进而掌握。

 

# 二分查找模板一

例题 1：「力扣」第 704 题：[二分查找](https://leetcode-cn.com/problems/binary-search/)。

> 给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target`，写一个函数搜索 `nums` 中的 `target`，如果目标值存在返回下标，否则返回 `-1`。

思路：

+ 在一个有序数组里查找元素，特别像以前电视「猜价格」上的猜价格游戏：运气好，一下子猜中，如果主持人说猜高了，下一步就应该往低了猜，如果主持人说猜低了，下一步就应该就往高了猜；

我们把待搜索区间的左边界下标设置为 `left`，右边界下标设置为 ` right` 。

+ 这个思路把待搜索区间 `[left, right]` 分为 3 个部分：
    +  `mid` 位置（只有 1 个元素）；
    +  `[left, mid - 1]` 里的所有元素；
    +  `[mid + 1, right]` 里的所有元素；

于是，二分查找就是不断地在区间 `[left, right]` 里根据 `left` 和 `right` 的中间位置 `mid = (left + right) / 2` 的元素大小，也就是看 `nums[mid]` 与 `target` 的大小关系：

+ 如果 `nums[mid] == target` ，返回 `mid`；
+ 如果 `nums[mid] > target` ，由于数组有序，`mid` 以及 `mid` 右边的所有元素都大于 `target`，目标元素一定在区间 `[left, mid - 1]` 里，因此设置 `right = mid - 1`；
+ 如果 `nums[mid] < target` ，由于数组有序，`mid` 以及 `mid` 左边的所有元素都小于 `target`，目标元素一定在区间 `[mid + 1, right]` 里，因此设置 `left = mid + 1`。

<!-- tabs:start -->

#### **Java**

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
            // 为了防止 left + right 整形溢出，写成如下形式
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

#### **C++**

```cpp
class Solution {
   public:
    int search(vector<int> &nums, int target) {
        // 特殊用例判断
        int len = nums.size();
        if (len == 0) {
            return -1;
        }
        // 在 [left, right] 区间里查找 target
        int left = 0;
        int right = len - 1;
        while (left <= right) {
            // 为了防止 left + right 整形溢出，写成如下形式
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
};
```

#### **Python3**

```python
class Solution(object):

    def search(self, nums: List[int], target: int) -> int:
        # 特殊用例判断
        n = len(nums)
        if n == 0:
            return -1
        # 在 [left, right] 区间里查找target
        left, right = 0, n - 1
        while left <= right:
            # 为了防止 left + right 整形溢出，写成如下形式
            # Python 使用 BigInteger，所以不用担心溢出，但还是推荐使用如下方式
            mid = left + (right - left) // 2

            if nums[mid] == target:
                return mid
            elif nums[mid] > target:
                # 下一轮搜索区间：[left, mid - 1]
                right = mid - 1
            else:
                # 此时：nums[mid] < target
                # 下一轮搜索区间：[mid + 1, right]
                left = mid + 1
        return -1
```

#### **javascript**

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var search = function(nums, target) {
  // 特殊用例判断
  let len = nums.length
  if (len === 0) {
    return -1
  }
  // 在 [left, right] 区间里查找 target
  let left = 0
  let right = len - 1
  while (left <= right) {
    // 为了防止 left + right 整形溢出，写成如下形式
    let mid = left + ((right - left) >> 1)
    if (nums[mid] === target) {
      return mid
    } else if (nums[mid] > target) {
      // 下一轮搜索区间：[left, mid - 1]
      right = mid - 1
    } else {
      // 此时：nums[mid] < target
      // 下一轮搜索区间：[mid + 1, right]
      left = mid + 1
    }
  }
  return -1
};
```
#### **Go**

```go
func search(nums []int, target int) int {
	//特殊拥立判断
	n:=len(nums)
	if n==0{
		return 0
	}
	// 在 [left, right] 区间里查找 target
	left:=0
	right:=n-1
	for left<=right{
		// 为了防止 left + right 整形溢出，写成如下形式
		mid:=left+(right-left)/2
		if nums[mid]==target{
			return mid
		}else if nums[mid]<target{
			// 下一轮搜索区间：[left, mid - 1]
			left=mid+1
		}else {
			// 此时：nums[mid] < target
			// 下一轮搜索区间：[mid + 1, right]
			right=mid-1
		}
	}
	return -1
}
```
<!-- tabs:end -->

注意事项：

+ 许多刚刚写的朋友，经常在写  `left = mid + 1;` 还是写 `right = mid - 1;` 上感到困惑，一个行之有效的思考策略是：**永远去想下一轮目标元素应该在哪个区间里：**
    + 如果目标元素在区间 `[left, mid - 1]` 里，就需要设置设置 `right = mid - 1`；
    + 如果目标元素在区间 `[mid + 1, right]` 里，就需要设置设置 `left = mid + 1`；

考虑不仔细是初学二分法容易出错的地方，这里切忌跳步，需要仔细想清楚每一行代码的含义。

+ 二分查找算法是典型的「减治思想」的应用，我们使用二分查找将待搜索的区间逐渐缩小，以达到「缩减问题规模」的目的；
+ 循环可以继续的条件是 `while (left <= right)`，特别地，当 `left == right` 即当待搜索区间里只有一个元素的时候，查找也必须进行下去；
+ `int mid = (left + right) / 2;` 在 `left + right` 整形溢出的时候，`mid` 会变成负数，回避这个问题的办法是写成 `int mid = left + (right - left) / 2;`。

 

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

#### **Javascript**

```javascript
function search (nums, left, right, target) {
    while (left < right) {
        // 选择中位数时下取整
        let mid = left + ((right - left) >> 1)
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

#### **Go**

```go
func search(nums []int, left, right, target int) {
	for left < right {
		// 选择中位数时下取整
		mid := left + (right-left)/2
		if check(mid) {
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

#### **Javascript**

```javascript
function search (nums, left, right, target) {
    while (left < right) {
        // 选择中位数时上取整
        let mid = left + ((right - left + 1) >> 1)
        if (check(mid)) {
            // 下一轮搜索区间是 [left, mid - 1]
            right = mid - 1
        } else {
            // 下一轮搜索区间是 [mid, right]
            left = mid
        }
    }
    // 退出循环的时候，程序只剩下一个元素没有看到。
    // 视情况，是否需要单独判断 left（或者 right）这个下标的元素是否符合题意
}
```

#### **Go**

```go
func search(nums []int, left, right, target int) {
	for left < right {
		// 选择中位数时上取整
		mid := left + (right-left+1)/2
		if check(mid) {
			// 下一轮搜索区间是 [left, mid - 1]
			right = mid - 1
		} else {
			// 下一轮搜索区间是 [mid, right]
			left = mid
		}
	}
	// 退出循环的时候，程序只剩下一个元素没有看到。
	// 视情况，是否需要单独判断 left（或者 right）这个下标的元素是否符合题意
}
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

#### **Javascript**

```javascript
function search (nums, left, right, target) {
    while (left + 1 < right) {
        // 选择中位数时下取整
        let mid = left + ((right - left) >> 1)
        if (nums[mid] === target) {
            return mid
        } else if (nums[mid] < target) {
            left = mid
        } else {
            right = mid
        }
    }

    if (nums[left] === target) {
        return left
    }
    if (nums[right] === target) {
        return right
    }
    return -1
}
```

#### **Go**

```go
func search(nums []int, left, right, target int) int {
	for left+1 < right {
		// 选择中位数时下取整
		mid := left + (right-left)/2
		if nums[mid] == target {
			return mid
		} else if nums[mid] < target {
			left = mid
		} else {
			right = mid
		}
	}
	//单独判断 left（或者 right）这个下标的元素是否符合题意
	if nums[left] == target {
		return left
	}
	if nums[right] == target {
		return right
	}
	return -1
}
```
<!-- tabs:end -->

 

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

#### **Javascript**

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var searchInsert = function(nums, target) {
    let len = nums.length
    if (len === 0) {
       return 0
    }
    // 特判
    if (nums[len - 1] < target) {
        return len
    }
    let left = 0
    let right = len - 1
    while (left < right) {
        let mid = left + ((right - left) >> 1)
        // 严格小于target 的元素一定不是解
        if (nums[mid] < target) {
            // 下一轮搜索区间是[mid + 1, right]
            left = mid + 1
        } else {
            right = mid
        }
    }
    return left
}

```

#### **Go**

```go
func searchInsert(nums []int, target int) int {
	n := len(nums)
	if n == 0 {
		return 0
	}
	// 特判
	if nums[n-1] < target {
		return n
	}
	left := 0
	right := n - 1
	for left < right {
		mid := left + (right-left)/2
		// 严格小于 target 的元素一定不是解
		if nums[mid] < target {
			// 下一轮搜索区间是 [mid + 1, right]
			left = mid + 1
		} else {
			right = mid
		}
	}
	return left
}
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

#### **Javascript**

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var searchInsert = function(nums, target) {
    let len = nums.length
    if (len === 0) {
       return 0
    }
    let left = 0
    // 因为有可能数组的最后一个元素的位置的下一个是我们要找的，故右边界是 len
    let right = len
    while (left < right) {
        let mid = left + ((right - left) >> 1)
        // 严格小于 target 的元素一定不是解
        if (nums[mid] < target) {
            // 下一轮搜索区间是 [mid + 1, right]
            left = mid + 1
        } else {
            right = mid
        }
    }
    return left
}

```

#### **Go**

```Go
func searchInsert(nums []int, target int) int {
	n := len(nums)
	if n == 0 {
		return 0
	}
	left := 0
	// 因为有可能数组的最后一个元素的位置的下一个是我们要找的，故右边界是 len
	right := n
	for left < right {
		mid := left + (right-left)/2
		// 严格小于 target 的元素一定不是解
		if nums[mid] < target {
			// 下一轮搜索区间是 [mid + 1, right]
			left = mid + 1
		} else {
			right = mid
		}
	}
	return left
}
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

#### **Javascript**

```javascript
/**
 * @param {number} x
 * @return {number}
 */
var mySqrt = function(x) {
    if (x === 0) {
        return 0
    }
    if (x === 1) {
        return 1
    }
    let left = 1
    let right = Math.floor(x / 2)
    while (left < right) {
        let mid = left + ((right - left + 1) >> 1)
        // 不使用mid * mid > x, 防止overflow
        if (mid > x / mid) {
            // 下一轮搜索的区间是[left, mid - 1]
            right = mid - 1
        } else {
            // 下一轮搜索的区间是[mid, right]
            left = mid
        }
    }
    return left
};
```

#### **Go**

```go
func mySqrt(x int) int {
	if x == 0 {
		return 0
	}
	if x == 1 {
		return 1
	}
	left := 1
	right := x / 2
	for left < right {
		mid := left + (right-left+1)/2
		// 不使用 mid * mid > x，防止 overflow
		if mid > x/mid {
			// 下一轮搜索的区间是 [left, mid - 1]
			right = mid - 1
		} else {
			// 下一轮搜索的区间是 [mid, right]
			left = mid
		}
	}
	return right
}
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

#### **Javascript**

```javascript
/**
 * @param {number[]} piles
 * @param {number} H
 * @return {number}
 */
var minEatingSpeed = function(piles, H) {
    let maxVal = 1
    piles.forEach(pile => {
        maxVal = Math.max(maxVal, pile)
    })
    // 速度最小的时候, 耗时最长
    let left = 1
    // 速度最大的时候, 耗时最短
    let right = maxVal
    while (left < right) {
        let mid = left + ((right - left) >> 1)
        if(calculateSum(piles, mid) > H) {
            // 耗时太多, 说明速度太慢了, 下一轮搜索区间在 [mid + 1, right]
            left = mid + 1
        } else {
            right = mid
        }
    }
    return left
};
/**
 * 如果返回的小时数严格大于 H，就不符合题意
 * @param {number[]} piles
 * @param {number} speed
 * @return {number} 需要的小时数
 */
function calculateSum(piles, speed) {
    let sum = 0
    piles.forEach(pile => {
        // 向上取整
        sum += Math.ceil(pile / speed)
    })
    return sum
}
```

<!-- tabs:end -->

 

# 精选练习

## 题型 1：在半有序（旋转有序或者是山脉）数组里查找元素

做这部分问题，需要摒弃一个观点：「二分查找」不是只能应用在有序数组里，只要是可以使用「减治思想」的问题，都可以使用二分查找。



| 题目                                                         | 提示                                   |
| ------------------------------------------------------------ | -------------------------------------- |
| [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)（必做） | 非常好的使用模板二的练习。             |
| [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)（必做） |                                        |
| [81. 搜索旋转排序数组 II](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/) |                                        |
| [153. 寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)（必做） |                                        |
| [154. 寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/) |                                        |
| [300. 最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/) | 二分只是其中一个步骤，本质是动态规划。 |
| [275. H指数 II](https://leetcode-cn.com/problems/h-index-ii/) |                                        |
| [1095. 山脉数组中查找目标值](https://leetcode-cn.com/problems/find-in-mountain-array/) |                                        |

## 题型 2：确定一个有范围的整数

| 题目                                                         | 提示               |
| ------------------------------------------------------------ | ------------------ |
| [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)（必做） | 需要结合抽屉原理。 |
| [374. 猜数字大小](https://leetcode-cn.com/problems/guess-number-higher-or-lower/) |                    |

## 题型 3：需要查找的目标元素满足某个特定的性质

| 题目                                                         | 提示                                 |
| ------------------------------------------------------------ | ------------------------------------ |
| [4. 寻找两个有序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/) | 一个非常难的问题，需要查资料弄清楚。 |
| [278. 第一个错误的版本](https://leetcode-cn.com/problems/first-bad-version/) |                                      |
| [410. 分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/)（必做） |                                      |
| [658. 找到 K 个最接近的元素](https://leetcode-cn.com/problems/find-k-closest-elements/) |                                      |
| [1300. 转变数组后最接近目标值的数组和](https://leetcode-cn.com/problems/sum-of-mutated-array-closest-to-target/) |                                      |

（本文完）

