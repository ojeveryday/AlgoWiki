# 堆的构建和堆排序

在我们做算法题的大多数情况，不需要手动地构建堆这个数据结构。通常我们直接使用编程语言自带的实现，如java使用PriorityQueue。不过，在学习堆这个数据结构的时候，手动构建堆，可以加深理解，帮助我们更好地使用它。

### 堆(最大堆)的构建

1. 在介绍章节中我们讲过堆的构建要通过一维数组来实现，那么如何维持一维数组中的逻辑关系呢？这里用到3个辅助方法, 来计算父节点和子节点的位置关系。

<!-- tabs:start -->
#### **Java**
```java

// 通过当前位置求父节点位置
public int parent(int index){
    return arr[(index - 1) / 2];
}

// 通过当前位置求左孩子节点位置
public int leftChild(int index){
    return arr[index * 2 + 1];
}

// 通过当前位置求右孩子节点位置
public int rightChild(int index){
    return arr[index * 2 + 2];
}

```
<!-- tabs:end -->

2. 向堆中添加元素和SiftUp

`SiftUp`操作是为了向堆中添加元素，和直接对数组`heapify`(将一个数组堆化)不同的是，`SiftUp`用于动态地构造一个堆。将从数组尾部添加的新元素给移动到正确的位置。

<!-- tabs:start -->
#### **Java**
```java

/**
* 先从数组尾部添加新元素，再将该元素移动到正确的位置。而这个移动的过程就叫做siftUp。
*/
public void add(int num){
    data.add(num);
    siftUp(num);
}

// siftUp 通过比较当前节点和父节点的大小，将较大的孩子节点交换到父亲节点的位置。同时不要忘记交换后节点位置也要更新为原父节点的位置。
public void siftUp(int index){
    while(data.get(index) > data.get(parent(index))){
        swap(data, index, parent(index));
        index = parent(index);
    }
}

```
<!-- tabs:end -->

3. 从堆中取出元素和SiftDown

<!-- tabs:start -->
#### **Java**

```java
/**
取出数组顶部元素，然后将顶部元素和数组最后一个元素swap，移除数组末尾的元素
*/
public int extractMax(){
    int maxVal = data.get(0);
    swap(data, 0, data.size() - 1);
    data.remove(data.size() - 1);
    return maxVal;
}
/**
     * 
     * @param index
     */
public void siftDown(int index){
    while(leftChild(index) < data.size()) {
        int j = leftChild(index);
        if (j + 1 < data.size() && data.get(j + 1) > data.get(j)) {
            j++;
        }
        if (data.get(index) > data.get(j)) {
            return;
        }
        swap(data, index, j);
        index = j;
    }
}

```

<!-- tabs:end -->


## 堆排序

+ 堆排序的原理是：假如要对一个数组进行从小到大的排列，则对数组`heapify`成一个最大堆。然后，每次将堆顶元素放到数组末尾，使`size`减一。当每次取出的当前最大元素按照`size`递减的位置排列后，就得到了一个有序的数组。

<!-- tabs:start -->
#### **Java**

```java
public class Solution {

    /**
     * 这里的heapsort是一种in-place排序，就是直接对原数组进行位置上的交换来排序
     * @param nums
     */
    public void heapSort(int[] nums) {
        int size = nums.length - 1;
        // 先对数组进行最大堆化
        heapify(nums, size);
        // 然后每次将堆顶的最大元素移动到数组末尾，将交换至堆顶的较小元素再进行下沉操作，使数组再次堆化有序。使size减一，堆化的操作将不影响末尾已经排序好的元素。重复操作直到堆中元素都按照从大到小，从后往前的方式排列在原数组上。
        while(size > 0){
            swap(nums, 0, size);
            siftDown(nums, 0, size);
            size --;
        }
    }

    //堆化，从末尾开始将较小元素下沉
    public void heapify(int[] nums, int size){
        for(int i = parent(size); i > 0; i--){
            siftDown(nums, i, size);
        }
    }

    //下沉操作
    public void siftDown(int[] nums, int index, int size){
        while(leftChild(index) < size){
            // 这里声明的j变量是用来选择当前节点的两个左右孩子的较大节点
            int j = leftChild(index);
            if(j + 1 < size && nums[j + 1] > nums[j]){
                j++;
            }
            // 如果两个孩子节点的较大值都比当前节点小，则不需要继续swap，直接return
            if(nums[index] > nums[j]){
                return;
            }
            // 如果较大的那个比当前节点大，则交换
            swap(nums, index, j);
            // 当前节点的index也要移动到被交换的孩子节点上，为下一次比较做准备
            index = j;
        }
    }

    // 对原数组进行操作，交换两个位置的变量
    public void swap(int[] nums, int i, int j){
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }

    // 输入当前节点的位置，计算得到父节点的位置
    public int parent(int index){
        return (index - 1) / 2;
    }

    // 计算左孩子节点的位置
    public int leftChild(int index){
        return index * 2 + 1;
    }

    // 计算右孩子节点的位置
    public int rightChild(int index){
        return index * 2 + 2;
    }
}

```

<!-- tabs:end -->

**复杂度分析**：

+ 时间复杂度：$O(N\log N)$，这里 $N$ 是数组的长度，每次抽取出堆顶为$O(\log N)$，$N$个元素就N次操作。
+ 空间复杂度：$O(1)$。in-place排序，不需要额外空间。