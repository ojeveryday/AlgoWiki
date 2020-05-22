# 双堆法（大小堆法）

双堆法用于解决的题型有：中位数问题。

1. 中位数问题  
例题 1: 「力扣」第 295 题 」：[数据流的中位数(hard)](https://leetcode-cn.com/problems/find-median-from-data-stream/)

`问题分析`：   
当需要求一组数的中位数的时候，我们需要先将该组数排序，然后找到中间位置。假如数组的长度是奇数，则返回中间那个数。假如数组长度为偶数，则返回中间两个数字和的一半。

这是非常自然想到的思路。

那么，如果这个数组是不断增加的呢？
我们会想到，新元素插入到排序数组里，然后再计算中位数对应下标的位置，就可以得到结果了。  
这是没有错的。不过，我们同时知道，在一个有序数组中插入一个值，时间复杂度为$O(N)$。这是因为，数组中插入一个元素，要挪动插入位置之后的所有数字。我们插入N次，大概可以得到$O(N^2)$的复杂度。有没有每次插入更快一点的数据结构呢。很显然，就是堆。每次插入的时间复杂度为$O(logN)$，N次插入大约为$O(NlogN)$。

双堆法的思想在于，将数组划分为大小相差不超过1的两个堆，保持这样的平衡。两个堆的顶部就会恰好是数组中间的值。

### 求中位数
基于双堆，我们可以很快写出求中位数的方法。

+ 首先，如果两个堆都为空，则返回0.0。然后，如果最小堆为空，（第二个if已经排除了均为空的情况），则返回最大堆。最后，（都不为空）如果size相等，就是偶数，不相等，就是奇数。返回对应的答案即可。
+ 注意，为偶数的情况，我们的中位数计算还是要使用经典的写法：先减后加来防止溢出。

<!-- tabs:start -->
#### **Java**

```java

public double findMedian() {
    if (maxHeap.isEmpty() && minHeap.isEmpty()) {
        return 0.0;
    }
    if (minHeap.isEmpty()) {
        return maxHeap.peek();
    }
    if (maxHeap.size() == minHeap.size()) {
        return maxHeap.peek() + (minHeap.peek() - maxHeap.peek()) / 2.0;
    } 
    return maxHeap.peek();
}

```
<!-- tabs:end -->

### 添加元素

+ 基于刚写好的findMedian()方法，我们的添加操作非常轻松。如果要添加的值大于原数组的中位数，我们就把它放到较大的那一组，也就是最小堆里面。如果小于或者等于中位数，就把它放到最大堆里面。
+ 这个时候，我们不要忘记了一件事。就是，每次添加了新的元素，我们的中位数在数组中的位置是会向右偏移的，所以我们要平衡一下两个堆里面元素的个数。

<!-- tabs:start -->
#### **Java**

```java

public void addNum(int num) {
    if (num <= findMedian()) {
        maxHeap.add(num);
    } else {
        minHeap.add(num);
    }
    balance();
}

```
<!-- tabs:end -->

+ 这个balance的过程非常简单，就是把多了元素的堆，堆顶元素给弹出加入到另一个堆中。即可。
+ 注意，我们这里的实现，默认是最大堆可以比最小堆多一个元素的。

<!-- tabs:start -->
#### **Java**

```java

public void balance() {
    if (maxHeap.isEmpty() && minHeap.isEmpty()) {
        return;
    }
    while (minHeap.size() > maxHeap.size()) {
        maxHeap.add(minHeap.poll());
    }
    while (maxHeap.size() - minHeap.size() > 1) {
        minHeap.add(maxHeap.poll());
    }
}

```
<!-- tabs:end -->

### 完整代码
<!-- tabs:start -->
#### **Java**

```java

class MedianFinder {

    PriorityQueue<Integer> minHeap = null;
    PriorityQueue<Integer> maxHeap = null;


    public MedianFinder() {
        // java 中默认的实现是最小堆
        minHeap = new PriorityQueue<>();
        maxHeap = new PriorityQueue<>((a , b) -> b - a);
    }

    public void addNum(int num) {
        if (num <= findMedian()) {
            maxHeap.add(num);
        } else {
            minHeap.add(num);
        }
        balance();
    }

    public void balance() {
        if (maxHeap.isEmpty() && minHeap.isEmpty()) {
            return;
        }
        while (minHeap.size() > maxHeap.size()) {
            maxHeap.add(minHeap.poll());
        }
        while (maxHeap.size() - minHeap.size() > 1) {
            minHeap.add(maxHeap.poll());
        }
    }

    public double findMedian() {
        if (maxHeap.isEmpty() && minHeap.isEmpty()) {
            return 0.0;
        }
        if (minHeap.isEmpty()) {
            return maxHeap.peek();
        }
        if (maxHeap.size() == minHeap.size()) {
            return maxHeap.peek() + (minHeap.peek() - maxHeap.peek()) / 2.0;
        }
        return (double) maxHeap.peek();
    }

    // public static void main(String[] args) {
    //     MedianFinder finder = new MedianFinder();
    //     System.out.println(finder.findMedian());
    //     finder.addNum(1);
    //     finder.addNum(2);
    //     finder.addNum(3);
    //     finder.addNum(4);
    //     System.out.println(finder.findMedian());
    // }
}

```
<!-- tabs:end -->