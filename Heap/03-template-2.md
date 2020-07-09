# 求(前/第）K(大/小/个)问题

当我们遇到题目中有优先级、大小关系或排序等字眼的时候，我们就可以想到堆这个数据结构。  


1. 第K大/小问题  
问题分析：这类问题求解的关键在于如何从无序的数组中，得到必须在有序前提下才能得到的第k个元素。
使无序变为有序的最简单方法就是排序，但是要得到我们需要的解却并不需要绝对有序。而堆，就可以使数组呈现一种整体有序的效果，从而更高效地求解问题。  

例题 1: 「力扣」第 215 题」：[数组中的第K个最大元素(easy)](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

+ 当我们求数组中第 `k` 大的数字的时候，首先会想到的是构建一个最大堆存储所有的元素，然后将前面 `k - 1` 个元素给弹出。最后返回堆顶。这确实是一个可行的办法。不过，反直觉的是，我们可以构建一个最小堆来更快地得到第k大的数字。

+ 因为，第 `k` 大的元素同时也是前 `k` 个最大元素中的最小值。这对应了我们一开始说的，最大最小想到堆。我们可以构建一个容量为 `k` 的最小堆来存储数组中最大的 `k` 个元素：当元素的数量超过 `k` 时，我们将堆顶最小的元素弹出，弹出不需要的 arr.length - `k` 个元素，剩下的就是我们需要的最大的 `k` 个元素，而堆顶也正式整个数组的第 `k` 大元素。

<!-- tabs:start -->
#### **Java**

```java

public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> minHeap = new PriorityQueue<>();

    for (Integer num : nums) {
        minHeap.add(num);
        if (minHeap.size() > k) {
            minHeap.poll();
        }
    }
    return minHeap.peek();
}

```
<!-- tabs:end -->

**复杂度分析**：

+ 时间复杂度： $O(N\log N)$ ， $N$ 是数组的长度。
+ 空间复杂度： $O(k)$ 。