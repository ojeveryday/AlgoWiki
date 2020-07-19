# Sort 排序（Java实现）
作者：youcoding98

审核：

排序算法是一种能将一串数据依照特定排序方式进行排列的一种算法。

排序算法足够简单，对于DFS，DP等来说，排序算法可以说是真正的入门算法。对于算法的初印象，就是大一C语言课上学的冒泡算法。如果觉得算法难，无法入手，不妨从写好一个排序算法开始。

这里带大家回顾一下常见排序算法的时间复杂度，空间复杂度：

【图片暂未上传】[先见排序算法中文维基](https://zh.wikipedia.org/wiki/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95)

而对于基础排序算法的复习，我就不一一介绍了，这里强推weiwei大佬的[复习基础排序算法（Java）](https://leetcode-cn.com/problems/sort-an-array/solution/fu-xi-ji-chu-pai-xu-suan-fa-java-by-liweiwei1419/)


排序的基本问题可见 「力扣」第 912 题：[排序数组](https://leetcode-cn.com/problems/sort-an-array/)

## 排序的应用

排序可以应用在：

1、 对时间复杂度和空间复杂度有要求的问题；

2、 归并区间的问题；

3、 涉及对数组的查找、删除等操作。

## 学习建议

对于算法学习，我们重要的是掌握其中的思想，毕竟万变不离其宗。也许算法题有千千万，只要我们掌握了这些基本排序的思想，编程将一气呵成！（大家共勉）


## 排序算法的模板
说明：这里提供的排序算法模板是针对不同情况的：

+ 模板一：针对单纯的排序问题，没有涉及其他情况
+ 模板二：对归并问题的处理
+ 模板三：抽屉思想解决时间复杂度O(n)

大家有不同的见解，欢迎指出，一起探讨。


### 一、排序算法模版之对基础排序算法的应用

例题 1：「力扣」第 164 题：[最大间距](https://leetcode-cn.com/problems/maximum-gap/)

> 给定一个无序的数组，找出数组在排序之后，相邻元素之间最大的差值。如果数组元素个数小于 `2`，则返回 `0`。
> 
> 请尝试在线性时间复杂度和空间复杂度的条件下解决此问题。

思路：

+ 题目就是普通的排序问题，但是由于题目中会对时间复杂度和空间复杂度做出要求，这时候就要求我们选择合适的排序算法进行求解。
+ 这个题中要求在线性时间复杂度和空间复杂度的条件下解决此问题，我们可选择的排序算法有桶排序，计数排序，计数排序等。
+ 针对这一类型的题目，没有唯一的模板，但是解题思路都很固定，选择合适的排序算法就能AC！！！

这里给出了线性时间复杂度和空间复杂度的排序算法的简单实现，方便大家理解算法思想

计数排序Java代码：

```java

public static void countSort(int[] arr){
       int max = Integer.MIN_VALUE;
       int min = Integer.MAX_VALUE;
       int len = arr.length;
       for (int i = 0; i < len; i++) {
           max = Math.max(max, arr[i]);
           min = Math.min(min, arr[i]);
       }
       //创建临时数组
       int[] temp = new int[max - min + 1];
       //计数
       for (int i = 0; i < len; i++) {
           temp[arr[i] - min] = temp[arr[i] - min] + 1;
       }

       //将临时数组中的数据依次放入原数组
       int index = 0;
       for (int i = 0; i < temp.length; i++) {
           int num = temp[i];
           while (num != 0){
               arr[index++] = i + min;
               num--;
           }
       }
 }

```

桶排序Java代码：

```java

   public static void bucketSort(int[] arr) {
        int max = Integer.MIN_VALUE;
        int min = Integer.MAX_VALUE;
        for (int i = 0; i < arr.length; i++) {
            max = Math.max(max, arr[i]);
            min = Math.min(min, arr[i]);
        }
        //桶数量的映射函数
        int bucketNum = (max - min) / arr.length + 1;
        //将整个桶数组用ArrayList表示，每个桶用存放Integer的ArrayList表示
        ArrayList<ArrayList<Integer>> bucketArr = new ArrayList<>(bucketNum);
        for (int i = 0; i < bucketNum; i++) {
            bucketArr.add(new ArrayList<>());
        }

        //根据映射函数，将同属于同一个桶的元素放入对应的桶里
        for (int i = 0; i < arr.length; i++) {
            int num = (arr[i] - min) / (arr.length);
            bucketArr.get(num).add(arr[i]);
        }

        //对桶进行排序(这里使用了java自带的TimSort算法)
        for (int i = 0; i < bucketArr.size(); i++) {
            Collections.sort(bucketArr.get(i));
        }

        int index = 0;
        for (ArrayList<Integer> list : bucketArr) {
            for (Integer integer : list) {
                arr[index ++] = integer;
            }
        }
    }

```

理解算法思想后，应该多做类似的题目进行巩固，在文章最后提供了一些类似的题目方便大家练习




### 二、排序算法模板之Merge合并

例题1 「力扣」第 56 题 [合并区间](https://leetcode-cn.com/problems/merge-intervals/)
> 给出一个区间的集合，请合并所有重叠的区间。

这个题想考察什么？

+ 合并所有重叠的区间，首先肯定是将所有区间进行从小到大的排序
+ 然后就是对区间进行合并操作，我们自然而然的可以想到归并排序算法的思想
+ 经过上面的分析，我们就可以把问题抽象成对多个有序数组进行合并操作

#####  接下来就是套用合并的模板了！

+ 对数组进行合并操作，那我们要新建一个`temp[]`来存放合并的内容，对于合并来说关键就在于区间之间元素的比较，所以我们要设置左右指针`left`，`righ`来对两个区间内的元素进行标记，然后对元素进行比较，最后将结果合并到`temp[]`中

下面是我总结的通用的合并模板：

```java

    int[] temp = new int[];	//可以是数组，也可以是链表，只是一个暂存空间	
    int left = 第一个数组元素的起点;
    int right = 第二个数组元素的起点;
    while (数组中仍还有元素){
		//对数组中元素进行比较
        if (nums1[left] < nums2[right]){
             temp[k++] = 合并结果；
        }
    }

```

接下来，我们把上面题目的思路套入模板，题目就能解决了。

```java

    //先将所有区间按从大到小排序
    Arrays.sort(intervals, Comparator.comparingInt(o -> o[0]));
    //新建暂存空间temp
    List<int[]> result = new ArrayList<>();
    result.add(intervals[0]);
    //进行循环，直到数组中还有元素
    for (int i = 1; i < intervals.length; i++) {
	//确定第二个要进行合并的数组
    	int[] temp = result.get(result.size() - 1);
	//确定区间的左右指针
    	int left = intervals[i][0];
    	int right = intervals[i][1];
	//对数组中元素进行比较
    	if (left > temp[1]){
	//存放合并结果
        	result.add(intervals[i]);
    	}else {
        	right = Math.max(right,temp[1]);
        	result.remove(result.size() - 1);
        	result.add(new int[]{temp[0], right});
    	}

    }
    return result.toArray(new int[0][]);

```



### 三、排序算法模板之抽屉思想解决时间复杂度O(n)
例题1 「力扣」第 448 题 [找到所有数组中消失的数字](https://leetcode-cn.com/problems/find-all-numbers-disappeared-in-an-array/)
> 给定一个范围在  1 ≤ a[i] ≤ n ( n = 数组大小 ) 的 整型数组，数组中的元素一些出现了两次，另一些只出现一次。找到所有在 [1, n] 范围之间没有出现在数组中的数字。

> 您能在不使用额外空间且时间复杂度为O(n)的情况下完成这个任务吗? 

+ 这题要是没有要求不使用额外空间就很好做了，但是它要求了，这就很难办了。说实话，刚一开始我也一筹莫展，看了weiwei大佬的[抽屉原理 + 基于“异或运算”交换两个变量的值](https://leetcode-cn.com/problems/find-all-numbers-disappeared-in-an-array/solution/tong-pai-xu-ji-yu-yi-huo-yun-suan-jiao-huan-liang-/)才恍然大悟，献上膝盖，顶礼膜拜。
+ 因为抽屉原理“一个萝卜一个坑”的思想与桶排序的思想有一点点相关，这里就把这类题归到了排序算法中。
+ 这里开始对这个题目进行分析：由于数组元素限定在数组长度的范围内，因此，我们可以通过一次遍历：让数值 `1` 就放在索引位置 `0` 处；让数值 `2` 就放在索引位置 `1` 处......
+ 经过一次遍历之后，如果数值不在对应的索引位置即`nums[i] != i + 1`，那么就是我们要找的没有出现在数组中的数字
+ 对于不适用额外空间，这里我们使用了异或运算来交换两个变量的值，不使用第三个变量

上模板：

```java

//将数值位置放到合适的索引位置
for (int i = 0; i < nums.length; i++) {
     	while (nums[nums[i] - 1] != nums[i]){
          	swap(nums,i,nums[i] - 1);
     	}
}
//存放结果内容
List<Integer> result = new ArrayList<>();
for (int i = 0; i < nums.length; i++) {
        if (对数组元素根据题意进行判断){
		result.add();
        }
}
//变量进行交换
private void swap(int[] nums, int index1, int index2) {
       if (index1 == index2) {
           return;
       }
       nums[index1] = nums[index1] ^ nums[index2];
       nums[index2] = nums[index1] ^ nums[index2];
       nums[index1] = nums[index1] ^ nums[index2];
}

```

接下来，我们把上面题目的思路套入模板，题目就能解决了。

```java

   public List<Integer> findDuplicates(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            while (nums[nums[i] - 1] != nums[i]){
                swap(nums,i,nums[i] - 1);
            }
        }
        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] - 1 != i){
            	   result.add(nums[i]);
            }
        }
        return result;
    }

    private void swap(int[] nums, int index1, int index2) {
        if (index1 == index2) {
            return;
        }
        nums[index1] = nums[index1] ^ nums[index2];
        nums[index2] = nums[index1] ^ nums[index2];
       	nums[index1] = nums[index1] ^ nums[index2];
    }

```

理解算法思想后，应该多做类似的题目进行巩固，下面提供了一些类似的题目方便大家练习

## 精选练习

### 题型 1：基本排序算法问题的求解（注意时间复杂度和空间复杂度）

「力扣」第 148 题 [排序链表](https://leetcode-cn.com/problems/sort-list/)：在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序。

「力扣」第 75 题 [颜色分类](https://leetcode-cn.com/problems/sort-colors/)：原地对它们进行排序

### 题型 2：归并问题，对区间进行合并
「力扣」第 88 题：[合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/) （简单）

「力扣」第 57 题： [插入区间](https://leetcode-cn.com/problems/insert-interval/)

「力扣」第 21 题： [合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

「力扣」第 23 题： [合并K个有序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)


### 题型 3：抽屉思想解决时间复杂度O(n)
「力扣」第 442 题：[数组中重复的数据](https://leetcode-cn.com/problems/find-all-duplicates-in-an-array/)

「力扣」第 41 题： [缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)





