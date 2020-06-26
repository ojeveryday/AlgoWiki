# 用链表模拟 Trie

## 模板

<!-- tabs:start -->

#### **C++**

```C++
struct Trie {
    int isEnd;
    Trie* next[26];
    Trie() {
        isEnd = 0;
        memset(next, 0, sizeof next);
    }
};

Trie* root = new Trie();

// 插入函数
void insert(const string& word) {
    Trie* node = root;
    for (char c : word) {
        if (node->next[c - 'a'] == nullptr) {
            node->next[c - 'a'] = new Trie();
        }
        node = node->next[c - 'a'];
    }
    node->isEnd++;
}

// 查询函数
bool search(const string& word) {
    Trie* node = root;
    for (const auto& w : word) {
        if (node->next[w - 'a'] == nullptr) return false;
        node = node->next[w - 'a'];
    }
    return node->isEnd;
}
```

<!-- tabs:end -->

## 练习题

「力扣」第 208 题：[实现Trie(前缀树)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)。

此题为实现一个 Trie 树，通过此题可以检验大家的理解程度。