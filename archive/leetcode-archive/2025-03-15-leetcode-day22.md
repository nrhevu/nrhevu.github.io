---
title: "LeetCode - Day 22"
date: 2025-03-15
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
208. Implement Trie (Prefix Tree)
Solved
Medium

Topics
Companies
A trie (pronounced as "try") or prefix tree is a tree data structure used to efficiently store and retrieve keys in a dataset of strings. There are various applications of this data structure, such as autocomplete and spellchecker.

Implement the Trie class:

Trie() Initializes the trie object.
void insert(String word) Inserts the string word into the trie.
boolean search(String word) Returns true if the string word is in the trie (i.e., was inserted before), and false otherwise.
boolean startsWith(String prefix) Returns true if there is a previously inserted string word that has the prefix prefix, and false otherwise.
 

Example 1:

Input
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
Output
[null, null, true, false, true, null, true]

Explanation
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // return True
trie.search("app");     // return False
trie.startsWith("app"); // return True
trie.insert("app");
trie.search("app");     // return True
 

Constraints:

1 <= word.length, prefix.length <= 2000
word and prefix consist only of lowercase English letters.
At most 3 * 104 calls in total will be made to insert, search, and startsWith.
```

# Solution
```python
class Node:
    def __init__(self):
        self.val = False
        self.next_node = {}

class Trie:

    def __init__(self):
        self.head = Node()
        
    def insert(self, word: str) -> None:
        node = self.head
        for w in word:
            if w not in node.next_node:
                node.next_node[w] = Node()

            node = node.next_node[w]

        node.val = True

    def search(self, word: str) -> bool:
        node = self.head
        for w in word:
            if w not in node.next_node:
                return False

            node = node.next_node[w]

        return node.val

    def startsWith(self, prefix: str) -> bool:
        node = self.head
        for w in prefix:
            if w not in node.next_node:
                return False

            node = node.next_node[w]

        return True


# Your Trie object will be instantiated and called as such:
# obj = Trie()
# obj.insert(word)
# param_2 = obj.search(word)
# param_3 = obj.startsWith(prefix)
```

# Submission
```

Runtime

39
ms
Beats
79.53%


Memory
32.18
MB
Beats
62.33%

```