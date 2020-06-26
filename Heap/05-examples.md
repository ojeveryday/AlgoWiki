# 精选例题

## 题型1： 前k个XX问题  
前 k 个元素的输出其实很简单，前 k 大的就构建最大堆输出 k 次。前 k 小的就构建最小堆输出 k 次。不过，在 leetcode 中，前 k 个 XX 问题常常和频率结合在一起，组成了一个先统计再排序的问题。


例题 1: 「力扣」：[692.前 K 个高频单词 (medium)](https://leetcode-cn.com/problems/top-k-frequent-words/)

思路： 先使用 hashmap 统计单词出现频率，再存入堆，每次输出堆顶。

<!-- tabs:start -->
#### **Java**

```java

class Solution {
    public List<String> topKFrequent(String[] words, int k) {

        HashMap<String, Integer> map = new HashMap<>();

        for (String word : words) {
            map.put(word, map.getOrDefault(word, 0) + 1);
        }

        PriorityQueue<String> heap = new PriorityQueue<>(
                (a, b) -> map.get(b).equals(map.get(a)) ? b.compareTo(a) : map.get(a) - map.get(b)
        );

        for (String key : map.keySet()) {
            heap.add(key);
            if (heap.size() > k) {
                heap.poll();
            }
        }

        LinkedList<String> res = new LinkedList<>();
        LinkedList<Integer> freq = new LinkedList<>();
        while (!heap.isEmpty()) {
            freq.add(map.get(heap.peek()));
            res.add(heap.poll());
        }

        Collections.reverse(res);
        return res;
    }
}


```

<!-- tabs:end -->

