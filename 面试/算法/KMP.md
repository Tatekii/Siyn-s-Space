KMP算法，全称为Knuth-Morris-Pratt算法，是一种用于在字符串中搜索子串的经典算法。它由Donald Knuth、Vaughan Pratt和James H. Morris在1977年共同提出，故名KMP算法。KMP算法的核心思想是利用已知信息，避免重复匹配，从而提高搜索效率。其时间复杂度为O(n + m)，其中n是主串的长度，m是模式串的长度。

### KMP算法的基本思想

KMP算法通过预处理模式串，构建一个部分匹配表（又称为失配函数或前缀函数），在搜索过程中利用这个表来避免重复比较，从而达到快速匹配的目的。

### 部分匹配表

部分匹配表（Partial Match Table, PMT）记录了模式串的每一个位置前缀和后缀的最长相同长度。该表用来在匹配失败时，指示模式串从哪里重新开始匹配。

#### 计算部分匹配表

设模式串为P，长度为m。部分匹配表PMT[i]表示P[0...i]的前缀集合与后缀集合的交集中最长元素的长度。

例如，对于模式串P = "ABABCABAA"，部分匹配表如下：
```javascript
i   0 1 2 3 4 5 6 7 8
P   A B A B C A B A A
PMT 0 0 1 2 0 1 2 3 1
```

### KMP搜索过程

1. **预处理**：计算模式串P的部分匹配表PMT。
2. **匹配**：用主串T和模式串P进行匹配，当发现匹配失败时，通过部分匹配表快速调整模式串的位置。

### 具体实现

以下是KMP算法的实现步骤：

#### 步骤1：计算部分匹配表
```python
def compute_partial_match_table(P):
    m = len(P)
    PMT = [0] * m
    j = 0  # length of the previous longest prefix suffix

    # the loop calculates PMT[i] for i from 1 to m-1
    for i in range(1, m):
        while (j > 0 and P[i] != P[j]):
            j = PMT[j - 1]

        if P[i] == P[j]:
            j += 1
            PMT[i] = j
        else:
            PMT[i] = 0

    return PMT
```

#### 步骤2：使用KMP算法进行字符串匹配
```python
def KMP_search(T, P):
    n = len(T)
    m = len(P)
    PMT = compute_partial_match_table(P)
    j = 0  # index for P

    for i in range(n):  # index for T
        while (j > 0 and T[i] != P[j]):
            j = PMT[j - 1]

        if T[i] == P[j]:
            j += 1

        if j == m:
            print(f"Found pattern at index {i - j + 1}")
            j = PMT[j - 1]
```

### 示例

假设主串T为 "ABABDABACDABABCABAB"，模式串P为 "ABABCABAB"。

1. 计算模式串P的部分匹配表PMT:
```
i   0 1 2 3 4 5 6 7 8
P   A B A B C A B A B
PMT 0 0 1 2 0 1 2 3 4
```

2. 使用KMP算法进行匹配：
```python
T = "ABABDABACDABABCABAB"
P = "ABABCABAB"
KMP_search(T, P)
```

输出结果：
```
Found pattern at index 10
```

这样，KMP算法在主串中快速找到了模式串的位置。通过利用部分匹配表，KMP算法在匹配失败时无需从头开始比较，而是根据已知的信息调整模式串的位置，从而显著提高了匹配效率。



```javascript
/**
 * 构建部分匹配表
 * @param {string} pattern - 模式串
 * @returns {number[]} 部分匹配表
 */
function buildPrefixTable(pattern) {
    const m = pattern.length;
    const prefixTable = new Array(m).fill(0);
    let j = 0;

    for (let i = 1; i < m; i++) {
        while (j > 0 && pattern[i] !== pattern[j]) {
            j = prefixTable[j - 1];
        }
        if (pattern[i] === pattern[j]) {
            j++;
        }
        prefixTable[i] = j;
    }

    return prefixTable;
}

/**
 * KMP字符串匹配算法
 * @param {string} text - 目标文本
 * @param {string} pattern - 模式串
 * @returns {number} 匹配的起始索引，如果没有匹配则返回-1
 */
function kmpSearch(text, pattern) {
    const n = text.length;
    const m = pattern.length;
    if (m === 0) return 0; // 空模式串默认匹配位置为0

    const prefixTable = buildPrefixTable(pattern);
    let j = 0;

    for (let i = 0; i < n; i++) {
        while (j > 0 && text[i] !== pattern[j]) {
            j = prefixTable[j - 1];
        }
        if (text[i] === pattern[j]) {
            j++;
        }
        if (j === m) {
            return i - m + 1; // 找到匹配，返回匹配的起始索引
        }
    }

    return -1; // 没有匹配
}

// 测试
const text = "ABABDABACDABABCABAB";
const pattern = "ABABCABAB";
const result = kmpSearch(text, pattern);
console.log(result); // 输出 10
```