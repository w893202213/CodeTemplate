# Trie 树刷题导向学习路线

> 目标：以刷题为导向，按照从易到难的 4 个梯度系统学习 Trie 树（字典树 / 前缀树）。  
> 推荐主线：OI-Wiki 学概念，洛谷刷中文题单与竞赛题，AcWing 补模板，力扣中文补面试题。  
> 前置建议：掌握数组模拟链式结构、字符串处理、二进制位运算、DFS、树上路径异或、基础 DP。

---

## 0. 学习入口：建议先收藏

| 网站 | 链接 | 用法 |
|---|---|---|
| OI-Wiki：字典树 Trie | https://oi-wiki.org/string/trie/ | 中文资料，适合学习 Trie 的定义、插入、查询、01 Trie 等基础 |
| OI-Wiki：可持久化 Trie | https://oi-wiki.org/ds/persistent-trie/ | 进阶资料，适合理解区间异或查询、历史版本 Trie |
| 洛谷 Trie 树基础练习题单 | https://www.luogu.com.cn/training/5061 | 中文题单，包含基础 Trie、01 Trie、技巧题和可持久化 Trie |
| AcWing 835 Trie 字符串统计 | https://www.acwing.com/problem/content/837/ | Trie 入门模板题，适合第一次手写数组版 Trie |
| AcWing 142 前缀统计 | https://www.acwing.com/problem/content/144/ | 前缀统计模板题 |
| AcWing 143 最大异或对 | https://www.acwing.com/problem/content/145/ | 01 Trie 入门模板题 |
| 力扣中文 Trie 相关题 | https://leetcode.cn/tag/trie/ | 面试导向题目，适合补充工程封装能力 |

---

# Trie 树到底解决什么问题？

Trie 树，又叫字典树、前缀树，主要用于维护字符串集合或二进制数集合。

它的核心思想是：

```text
把一个字符串或一个数的二进制表示拆成若干字符/二进制位，
从根节点开始逐层存储。
公共前缀只存一次，从而减少重复比较。
```

例如插入：

```text
abc
abd
abx
```

它们都会共享前缀：

```text
a -> b
```

因此 Trie 特别适合处理：

- 字符串插入；
- 字符串查询；
- 前缀统计；
- 自动补全；
- 单词替换；
- 通配符匹配；
- 最大异或值；
- 树上异或路径；
- 可持久化异或区间查询；
- AC 自动机。

---

# 一、基础入门：字符串 Trie 插入与查询

## 学习目标

这一阶段只学习最基础的字符串 Trie。

需要掌握：

- 数组模拟 Trie；
- 插入字符串；
- 查询字符串是否存在；
- 查询字符串出现次数；
- 记录单词结尾；
- 处理重复插入；
- 区分“完整单词存在”和“前缀存在”。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | 力扣 208 实现 Trie 前缀树 | https://leetcode.cn/problems/implement-trie-prefix-tree/ | 类封装版 Trie：insert、search、startsWith |
| 2 | AcWing 835 Trie 字符串统计 | https://www.acwing.com/problem/content/837/ | 数组版 Trie：插入字符串、查询出现次数 |
| 3 | 洛谷 P2580 于是他错误的点名开始了 | https://www.luogu.com.cn/problem/P2580 | 查询名字是否存在，并判断是否重复查询 |
| 4 | 洛谷 P8306 【模板】字典树 / Trie | https://www.luogu.com.cn/problem/P8306 | 统计有多少字符串以询问串为前缀 |
| 5 | 洛谷 P1059 明明的随机数 | https://www.luogu.com.cn/problem/P1059 | 非 Trie 题，可作为“什么时候不用 Trie”的对比题 |

## 基础模板：数组版 Trie

```cpp
const int N = 100000 + 10;

int son[N][26];
int cnt[N];
int idx = 0;

void insert(string s) {
    int p = 0;
    for (char ch : s) {
        int u = ch - 'a';
        if (!son[p][u]) {
            son[p][u] = ++idx;
        }
        p = son[p][u];
    }
    cnt[p]++;
}

int query(string s) {
    int p = 0;
    for (char ch : s) {
        int u = ch - 'a';
        if (!son[p][u]) {
            return 0;
        }
        p = son[p][u];
    }
    return cnt[p];
}
```

## 本阶段必须理解

### 1. 节点不一定代表单个字符

Trie 中的边通常代表字符，节点代表从根到该节点形成的前缀。

例如路径：

```text
root -> a -> b -> c
```

表示字符串：

```text
abc
```

### 2. `cnt[p]` 的含义要明确

常见含义有两种：

```text
cnt[p] = 有多少个完整单词在节点 p 结束。
```

或者：

```text
cnt[p] = 有多少个字符串经过节点 p。
```

这两种写法都常见，但不能混用。

### 3. `search` 和 `startsWith` 不一样

```text
search("app")：要求 app 是完整单词。
startsWith("app")：只要求存在某个单词以 app 开头。
```

## 本阶段常见错误

- 没有在单词结尾处打标记；
- 查询前缀时误用了完整单词查询逻辑；
- 重复插入时没有累计次数；
- 节点数组开小，导致越界；
- 字符集不是 26 个小写字母，却仍然使用 `son[][26]`；
- 多组测试数据时没有清空 Trie。

---

# 二、简单应用：前缀统计、单词检索与通配符

## 学习目标

这一阶段开始处理“前缀关系”和“词典检索”问题。

需要掌握：

- 统计某个字符串是多少个单词的前缀；
- 统计有多少单词是某个查询串的前缀；
- 记录单词出现在哪些文章中；
- Trie + DFS 处理通配符；
- 最短前缀替换；
- 词链问题。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | AcWing 142 前缀统计 | https://www.acwing.com/problem/content/144/ | 查询有多少字符串是查询串的前缀 |
| 2 | 洛谷 P2922 [USACO08DEC] Secret Message G | https://www.luogu.com.cn/problem/P2922 | 二进制 Trie + 前缀匹配计数 |
| 3 | 洛谷 P3879 [TJOI2010] 阅读理解 | https://www.luogu.com.cn/problem/P3879 | 单词检索，记录单词出现在哪些文章中 |
| 4 | 洛谷 P1481 魔族密码 | https://www.luogu.com.cn/problem/P1481 | 前缀链，统计最长词链 |
| 5 | 力扣 211 添加与搜索单词 | https://leetcode.cn/problems/design-add-and-search-words-data-structure/ | Trie + DFS，支持 `.` 通配符 |
| 6 | 力扣 648 单词替换 | https://leetcode.cn/problems/replace-words/ | Trie 查找最短词根 |
| 7 | 力扣 677 键值映射 | https://leetcode.cn/problems/map-sum-pairs/ | Trie 节点维护前缀权值和 |

## 前缀统计的两种方向

### 方向一：查询“某个串是多少个单词的前缀”

例如给定词典：

```text
apple
app
application
banana
```

询问：

```text
app
```

答案是：

```text
3
```

因为：

```text
app 是 app、apple、application 的前缀。
```

常见做法：

```text
插入时，每经过一个节点，就把 pass[p]++。
查询时，走到查询串末尾，返回 pass[p]。
```

### 方向二：查询“有多少个单词是某个串的前缀”

例如词典：

```text
a
ab
abc
b
```

询问：

```text
abcd
```

答案是：

```text
3
```

因为：

```text
a、ab、abc 都是 abcd 的前缀。
```

常见做法：

```text
查询时从根沿着查询串往下走，
每到一个节点，如果这个节点是单词结尾，就累加。
```

## 通配符搜索

力扣 211 中，`.` 可以匹配任意小写字母。

普通查询：

```cpp
search("bad")
```

只需要沿着固定路径走。

通配符查询：

```cpp
search("b.d")
```

遇到 `.` 时，需要枚举当前节点的所有儿子，并递归搜索。

核心思想：

```text
固定字符：沿着唯一儿子走。
通配符：枚举所有可能儿子。
```

## 本阶段常见错误

- 没有区分 `pass[p]` 和 `end[p]`；
- 前缀统计方向弄反；
- 通配符搜索时忘记回溯或递归返回；
- P3879 中同一个单词在同一篇文章多次出现时，没有去重；
- 最短前缀替换时没有在遇到第一个词根时立即停止。

---

# 三、中等难度：01 Trie 与最大异或问题

## 学习目标

这一阶段学习 01 Trie。

01 Trie 不是维护字符串，而是维护整数的二进制位。

每个节点只有两个儿子：

```text
0 儿子
1 儿子
```

它常用于解决最大异或值问题。

需要掌握：

- 把整数按二进制位插入 Trie；
- 查询与当前数异或最大的数；
- 从高位到低位贪心；
- 前缀异或；
- 树上路径异或。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | AcWing 143 最大异或对 | https://www.acwing.com/problem/content/145/ | 01 Trie 模板，求数组中两个数最大异或值 |
| 2 | 力扣 421 数组中两个数的最大异或值 | https://leetcode.cn/problems/maximum-xor-of-two-numbers-in-an-array/ | 面试版最大异或对 |
| 3 | 洛谷 P4551 最长异或路径 | https://www.luogu.com.cn/problem/P4551 | 树上路径异或 + 01 Trie |
| 4 | Codeforces 817E Choosing The Commander | https://codeforces.com/problemset/problem/817/E | 动态插入、删除、查询最大异或，英文题 |
| 5 | 洛谷 P4735 最大异或和 | https://www.luogu.com.cn/problem/P4735 | 可持久化 Trie 预备题，难度较高，可后置 |

## 01 Trie 基础模板

```cpp
const int N = 100000 + 10;
const int M = N * 31;

int son[M][2];
int idx = 0;

void insert(int x) {
    int p = 0;
    for (int i = 30; i >= 0; i--) {
        int u = (x >> i) & 1;
        if (!son[p][u]) {
            son[p][u] = ++idx;
        }
        p = son[p][u];
    }
}

int queryMaxXor(int x) {
    int p = 0;
    int res = 0;

    for (int i = 30; i >= 0; i--) {
        int u = (x >> i) & 1;
        int want = u ^ 1;

        if (son[p][want]) {
            res += (1 << i);
            p = son[p][want];
        } else {
            p = son[p][u];
        }
    }

    return res;
}
```

## 为什么从高位到低位？

因为二进制高位权值更大。

例如第 30 位如果能异或出 1，它的贡献是：

```text
2^30
```

远大于所有低位之和。

所以查询时策略是：

```text
当前位是 0，优先找 1；
当前位是 1，优先找 0。
```

也就是尽量让当前位异或结果为 1。

## 树上最长异或路径

洛谷 P4551 的关键转化：

```text
树上 u 到 v 路径的边权异或
=
xorRoot[u] ^ xorRoot[v]
```

其中：

```text
xorRoot[x] = 根节点到 x 的路径边权异或
```

因此问题转化为：

```text
在数组 xorRoot[] 中选两个数，使它们异或值最大。
```

这就变成了最大异或对问题，可以用 01 Trie 解决。

## 本阶段常见错误

- 从低位往高位插入，导致贪心失效；
- 位数开少，例如只开到 20 位但数据可到 `2^31`；
- 使用 `1 << 31` 导致 int 溢出；
- 没有插入第一个数就查询；
- 树上异或路径没有先转成根到节点的前缀异或；
- 动态删除时没有维护每个节点经过次数。

---

# 四、进阶问题：Trie + DP、可持久化 Trie、AC 自动机衔接

## 学习目标

这一阶段 Trie 不再只是模板，而是和其他算法结合。

重点方向：

- Trie + DP；
- Trie + 贪心；
- Trie + DFS 序；
- Trie + 堆；
- 可持久化 Trie；
- Trie 向 AC 自动机过渡。

| 方向 | 题目/资料 | 链接 | 训练点 |
|---|---|---|---|
| Trie + DP | 洛谷 P2292 [HNOI2004] L 语言 | https://www.luogu.com.cn/problem/P2292 | 字典匹配 + DP，判断最长可理解前缀 |
| 可持久化 Trie 资料 | OI-Wiki 可持久化 Trie | https://oi-wiki.org/ds/persistent-trie/ | 历史版本 Trie、区间异或查询 |
| 可持久化 Trie | 洛谷 P4735 最大异或和 | https://www.luogu.com.cn/problem/P4735 | 前缀异或 + 可持久化 Trie |
| 异或 Top-K | 洛谷 P5283 [十二省联考2019] 异或粽子 | https://www.luogu.com.cn/problem/P5283 | 前缀异或 + Trie + 堆，求前 k 大异或和 |
| AC 自动机入门 | 洛谷 P3808 【模板】AC 自动机（简单版） | https://www.luogu.com.cn/problem/P3808 | Trie + fail 指针，多模式串匹配 |
| AC 自动机资料 | OI-Wiki AC 自动机 | https://oi-wiki.org/string/ac-automaton/ | Trie 的重要扩展方向 |

## Trie + DP

以洛谷 P2292 为例：

题意大致是：

```text
给定一个字典，判断文章前缀能否被切分成若干字典单词。
```

可以这样建模：

```text
f[i] = 前 i 个字符是否可以被完全理解。
```

转移思路：

```text
如果 f[i] = true，
就从文章第 i + 1 位开始沿着 Trie 往下走。
每遇到一个单词结尾，就更新 f[j] = true。
```

这种题的关键是：

```text
Trie 负责快速匹配字典单词；
DP 负责判断字符串能否被切分。
```

## 可持久化 Trie

可持久化 Trie 常用于处理：

```text
区间内选一个数，使它与 x 异或最大。
```

普通 01 Trie 只能维护一个集合。

可持久化 Trie 维护的是：

```text
前缀版本 root[0], root[1], ..., root[n]
```

查询区间 `[l, r]` 时，用：

```text
root[r] - root[l - 1]
```

得到这个区间中的数集信息。

核心思想类似主席树：

```text
每次插入一个数，只新建被修改路径上的节点；
没有变化的部分沿用旧版本。
```

## AC 自动机衔接

AC 自动机可以理解为：

```text
Trie + fail 指针
```

普通 Trie 适合：

```text
查询一个词或前缀是否存在。
```

AC 自动机适合：

```text
在一个长文本中同时匹配多个模式串。
```

如果已经掌握 Trie，那么 AC 自动机就是自然的下一步。

## 本阶段常见错误

- Trie + DP 中没有从所有可达位置出发转移；
- 可持久化 Trie 中没有正确复制旧节点；
- 区间查询时没有用两个版本相减；
- 异或 Top-K 问题中重复统计同一对前缀；
- AC 自动机中没有正确构建 fail 指针；
- 把 Trie 的“树边”与 AC 自动机的“失配边”混为一谈。

---

# 推荐完整刷题路线

建议按照下面顺序推进：

```text
LeetCode 208
→ AcWing 835
→ P2580
→ P8306
→ AcWing 142
→ P2922
→ P3879
→ P1481
→ LeetCode 211
→ LeetCode 648
→ AcWing 143
→ LeetCode 421
→ P4551
→ P2292
→ P4735
→ P5283
→ P3808
```

如果时间有限，可以只刷核心路线：

```text
AcWing 835
→ P2580
→ P8306
→ AcWing 142
→ AcWing 143
→ P4551
→ P2292
→ P4735
```

---

# 每道题的复盘模板

建议每做完一道 Trie 题，都写下面几个问题。

## 1. Trie 维护的是什么？

例如：

```text
本题 Trie 维护的是小写字母字符串集合。
```

或者：

```text
本题 Trie 维护的是整数的二进制表示。
```

## 2. 节点上需要记录什么信息？

常见信息包括：

```text
end[p]：有多少个单词在节点 p 结束。
pass[p]：有多少个单词经过节点 p。
id[p]：节点对应的单词编号。
cnt[p]：当前节点子树中的有效元素数量。
root[i]：可持久化 Trie 的第 i 个版本根节点。
```

## 3. 查询的是完整单词、前缀，还是最大异或？

不同问题对应不同逻辑：

```text
完整单词查询：走完整个字符串后检查 end[p]。
前缀查询：只要路径存在即可。
前缀统计：返回 pass[p] 或沿途累加 end[p]。
最大异或：从高位到低位尽量走相反位。
```

## 4. 字符集大小是多少？

例如：

```text
小写字母：26
大小写字母 + 数字：62
二进制位：2
ASCII 字符：128 或 256
```

字符集大小决定 `son` 数组第二维。

## 5. 是否需要处理重复插入或删除？

如果有删除操作，需要维护：

```text
cnt[p] = 有多少有效元素经过节点 p
```

删除时沿路径减一。

查询时只有 `cnt[p] > 0` 的儿子才可以走。

---

# Trie 与其他结构的选择

## 更适合 Trie 的情况

- 大量字符串插入与查询；
- 前缀统计；
- 自动补全；
- 字符串集合中的公共前缀明显；
- 二进制最大异或；
- 需要按位贪心；
- 多模式串匹配的前置结构。

## 更适合哈希表的情况

- 只判断完整字符串是否出现；
- 不关心前缀；
- 字符串数量不大；
- 不需要维护字典序或前缀关系。

## 更适合排序 + 二分的情况

- 数据静态；
- 只需要离线查询；
- 查询量不大；
- 不想手写复杂结构。

## 更适合 AC 自动机的情况

- 一个长文本中同时匹配多个模式串；
- 需要统计模式串出现次数；
- Trie 只能解决“从根开始匹配”，而 AC 自动机可以处理文本中的任意位置匹配。

---

# 三类 Trie 的区别

## 1. 普通字符串 Trie

维护字符路径。

适合：

```text
单词查询
前缀统计
自动补全
通配符搜索
单词替换
```

代表题：

```text
AcWing 835
P2580
P8306
LeetCode 208
LeetCode 211
```

## 2. 01 Trie

维护整数二进制位。

适合：

```text
最大异或值
树上异或路径
动态异或集合
```

代表题：

```text
AcWing 143
LeetCode 421
P4551
CF817E
```

## 3. 可持久化 Trie

维护多个历史版本的 01 Trie。

适合：

```text
区间异或最大值
带历史版本的集合查询
前缀异或区间查询
```

代表题：

```text
P4735
P5283
```

---

# 最终掌握标准

学完 Trie 后，看到一道题应该能快速判断：

1. 题目是在维护字符串，还是维护二进制数？
2. 查询的是完整单词、前缀、前缀数量，还是最大异或？
3. 节点上需要维护 `end`、`pass`、`cnt`、编号还是版本信息？
4. 字符集大小是多少？
5. 是否有重复插入、删除、多组测试？
6. 01 Trie 是否要从高位到低位贪心？
7. 树上异或路径是否能转化成根到节点的前缀异或？
8. 是否需要可持久化 Trie 或 AC 自动机？

如果这些问题都能回答出来，说明你已经能把 Trie 当作一种建模工具，而不是只会写字符串插入和查询模板。
