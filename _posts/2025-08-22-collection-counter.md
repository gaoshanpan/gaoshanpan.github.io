---
title: "Collection counter"
date: 2025-08-22 10:00:00 +0800
categories: [CS basics, Python] #[Top_category, sub_category]
tags: [python, leetcode, collection] ## TAG names should always be lowercase
# author: 
---

`collections.Counter` 是 Python 内置模块 `collections` 中的一个非常实用的类，用于 **快速统计可迭代对象中元素出现的次数**。

---

## 基本用法

```python
from collections import Counter

nums = [1, 2, 2, 3, 3, 3, 4]

counter = Counter(nums)
print(counter)   # Counter({3: 3, 2: 2, 1: 1, 4: 1})
```

它返回的是一个类似字典的对象：

* **键 (key)** = 元素
* **值 (value)** = 出现的次数

---

## 常见操作

### 1. 访问元素计数

```python
print(counter[3])  # 3 出现了 3 次
print(counter[5])  # 没有出现，返回 0
```

### 2. 找出出现次数最多的元素

```python
print(counter.most_common(1))  
# [(3, 3)]   → 出现次数最多的一个元素及其次数
```

### 3. 找出前 k 个高频元素

```python
print(counter.most_common(2))  
# [(3, 3), (2, 2)]
```

### 4. 转换为普通字典

```python
print(dict(counter))  
# {1: 1, 2: 2, 3: 3, 4: 1}
```

### 5. 与算术操作结合

Counter 对象之间可以做加减并集交集：

```python
c1 = Counter(a=3, b=1)
c2 = Counter(a=1, b=2)

print(c1 + c2)  # Counter({'a': 4, 'b': 3})
print(c1 - c2)  # Counter({'a': 2})  (负数会被去掉)
```

---

## 应用：多数元素问题

结合你刚才的问题，可以直接用 `Counter`：

```python
from collections import Counter

def majorityElement(nums):
    counter = Counter(nums)
    return counter.most_common(1)[0][0]
```

---

 **一下是Counter 在算法题中的高频用法清单**
好的 ✅ 我来帮你系统整理 **`collections.Counter` 在算法题中的高频用法清单**，每个场景配上简洁示例。这样以后遇到题目你就能快速套用。

---

# 📌 `collections.Counter` 高频用法清单

## 1. 多数元素 / 高频元素

**题型**：找出出现次数最多的元素

```python
from collections import Counter

nums = [2, 2, 1, 1, 1, 2, 2]
counter = Counter(nums)
print(counter.most_common(1)[0][0])  # 输出 2
```

---

## 2. 前 K 个高频元素

**题型**：Top K 高频

```python
nums = [1,1,1,2,2,3]
k = 2
counter = Counter(nums)
print([x for x, _ in counter.most_common(k)])  # [1, 2]
# _ is a placeholder for unpacking, which is used to ignore an element(here is 元素个数)
# print(counter.most_common(k))
# [(1, 3), (2, 2)]
```

---

## 3. 字母异位词分组 (Anagrams)

**题型**：分组时用 Counter 作为 key（但要转为 tuple）

```python
words = ["eat", "tea", "tan", "ate", "nat", "bat"]
groups = {}
for w in words:
    key = tuple(sorted(Counter(w).items()))
    groups.setdefault(key, []).append(w)

print(list(groups.values()))
# [['eat', 'tea', 'ate'], ['tan', 'nat'], ['bat']]
```

---

## 4. 判断是否为字母异位词

**题型**：直接比较 Counter

```python
s, t = "anagram", "nagaram"
print(Counter(s) == Counter(t))  # True
```

---

## 5. 找缺失或多余元素

**题型**：两个数组差异

```python
s = [1,2,3,4]
t = [1,2,2,3,4]
print(list((Counter(t) - Counter(s)).elements()))  # [2] (多出来的元素)
```

---

## 6. 滑动窗口统计字符频率

**题型**：最小覆盖子串 / 字符匹配

```python
s = "ADOBECODEBANC"
t = "ABC"
need = Counter(t)
window = Counter()

# 在滑动窗口内更新 window 与 need 对比
```

---

## 7. 判断能否由另一字符串组成

**题型**：杂志拼信 (ransom note)

```python
ransomNote = "aa"
magazine = "aab"
print(not Counter(ransomNote) - Counter(magazine))  # True
```

---

## 8. 出现次数为奇数的元素

**题型**：数组中唯一未配对元素

```python
nums = [2,2,1,1,4]
counter = Counter(nums)
print([x for x in counter if counter[x] % 2 == 1])  # [4]
```

---

## 9. 最长回文串

**题型**：用 Counter 统计字母频率，计算能组成的最长回文长度

```python
s = "abccccdd"
counter = Counter(s)
length = sum(v//2*2 for v in counter.values())
if any(v % 2 for v in counter.values()):
    length += 1
print(length)  # 7
```

---

# 🎯 总结

`Counter` 常见题型分类：

1. **高频统计类**：多数元素、Top K 高频元素
2. **异位词类**：判断是否异位词、分组异位词
3. **差集类**：找缺失/多余元素、判断字符串能否组成
4. **滑动窗口类**：最小覆盖子串、子串匹配
5. **组合计数类**：最长回文串、奇偶统计

