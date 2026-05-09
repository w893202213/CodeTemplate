# Z 函数算法刷题导向学习路线

> 目标：以刷题为导向，按照从易到难的 4 个梯度系统学习 Z 函数（Z Algorithm / 扩展 KMP）。  
> 推荐主线：OI-Wiki 学定义与模板，洛谷 P5410 练 exKMP 模板，力扣中文补面试应用，Codeforces 补 border 与统计类题。  
> 前置建议：掌握字符串下标、暴力匹配、KMP 的前缀函数、前缀/后缀、border、字符串周期。

---

## 0. 学习入口：建议先收藏

| 网站 | 链接 | 用法 |
|---|---|---|
| OI-Wiki：Z 函数（扩展 KMP） | https://oi-wiki.org/string/z-func/ | 中文资料，系统学习 Z 函数定义、线性求法、字符串匹配和应用 |
| 洛谷 P5410【模板】扩展 KMP / exKMP（Z 函数） | https://www.luogu.com.cn/problem/P5410 | 中文模板题，要求求模式串自身 Z 数组，以及模式串和文本串每个后缀的 LCP |
| 力扣 2223 构造字符串的总得分和 | https://leetcode.cn/problems/sum-of-scores-of-built-strings/ | Z 函数经典面试题，统计所有后缀与原串的 LCP 总和 |
| 力扣 3031 将单词恢复初始状态所需的最短时间 II | https://leetcode.cn/problems/minimum-time-to-revert-word-to-initial-state-ii/ | Z 函数判断后缀是否等于前缀 |
| 力扣 1392 最长快乐前缀 | https://leetcode.cn/problems/longest-happy-prefix/ | 求最长相等真前缀真后缀，可用 KMP 或 Z 函数 |
| Codeforces 432D Prefixes and Suffixes | https://codeforces.com/problemset/problem/432/D | border + 出现次数统计，Z 函数或 KMP 都可做 |

---

# Z 函数到底解决什么问题？

Z 函数用于描述：

```text
从字符串每个位置 i 开始的后缀，与整个字符串的最长公共前缀长度。
```

设字符串为 `s`，长度为 `n`，则：

```text
z[i] = LCP(s, s[i...n-1])
```

通常规定：

```text
z[0] = 0
```

例如：

```text
s = aaaaa
z = [0, 4, 3, 2, 1]
```

Z 函数常用于：

- 单模式串匹配；
- 计算每个后缀与原串的 LCP；
- 求字符串总得分；
- 判断前缀是否等于后缀；
- 分析 border；
- 分析周期和循环节；
- 解决字符串拼接后的匹配问题；
- 与 KMP、exKMP、Z Algorithm 互相转换理解。

---

# Z 函数与 KMP 的区别

KMP 的前缀函数 `pi[i]` 关注：

```text
s[0...i] 的最长相等真前缀和真后缀长度。
```

Z 函数 `z[i]` 关注：

```text
s[i...n-1] 与整个 s 的最长公共前缀长度。
```

简单说：

```text
KMP / pi：看每个前缀内部的 border。
Z 函数：看每个后缀和整串前缀能匹配多长。
```

二者都能解决字符串匹配、border、周期问题，只是观察角度不同。

---

# 一、基础入门：Z 数组定义与模板题

## 学习目标

这一阶段只做 Z 函数基础模板。

需要掌握：

- `z[i]` 的含义；
- Z-box / 匹配窗口 `[l, r]`；
- 如何在线性时间求 Z 数组；
- 为什么右端点 `r` 可以复用已有匹配信息；
- 如何手算几个短字符串的 Z 数组。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | OI-Wiki Z 函数样例 | https://oi-wiki.org/string/z-func/ | 对照样例手算 Z 数组 |
| 2 | 洛谷 P5410【模板】扩展 KMP / exKMP（Z 函数） | https://www.luogu.com.cn/problem/P5410 | 标准模板题，求 Z 数组和扩展匹配数组 |
| 3 | 力扣 2223 构造字符串的总得分和 | https://leetcode.cn/problems/sum-of-scores-of-built-strings/ | 直接使用 Z 数组求所有后缀 LCP 总和 |
| 4 | 力扣 28 找出字符串中第一个匹配项的下标 | https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/ | 可用 Z 函数重写普通字符串匹配 |

## Z 函数模板

```cpp
vector<int> z_function(const string& s) {
    int n = s.size();
    vector<int> z(n);

    int l = 0, r = 0;

    for (int i = 1; i < n; i++) {
        if (i <= r) {
            z[i] = min(r - i + 1, z[i - l]);
        }

        while (i + z[i] < n && s[z[i]] == s[i + z[i]]) {
            z[i]++;
        }

        if (i + z[i] - 1 > r) {
            l = i;
            r = i + z[i] - 1;
        }
    }

    return z;
}
```

## Z-box 是什么？

如果当前位置 `i` 的匹配长度是 `z[i]`，那么区间：

```text
[i, i + z[i] - 1]
```

表示从 `i` 开始的一段子串，和字符串前缀相同。

Z 算法维护当前最靠右的匹配区间：

```text
[l, r]
```

如果新的位置 `i` 落在 `[l, r]` 内，就可以复用 `z[i - l]` 的信息。

## 本阶段常见错误

- 把 `z[i]` 理解成从 `0` 到 `i` 的前缀函数；
- 忘记 `z[0]` 通常设为 0；
- `min(r - i + 1, z[i - l])` 中下标写错；
- while 扩展时越界；
- 更新 `[l, r]` 时忘记减 1；
- 使用 1 下标和 0 下标模板时混乱。

---

# 二、字符串匹配：模式串 + 分隔符 + 文本串

## 学习目标

这一阶段学习 Z 函数最直接的应用：字符串匹配。

需要掌握：

- 拼接字符串；
- 选择不会出现在原串中的分隔符；
- 找到所有匹配位置；
- 统计出现次数；
- 处理重叠匹配；
- 与 KMP 模板互相对照。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | 力扣 28 找出字符串中第一个匹配项的下标 | https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/ | 模式串匹配，返回第一次出现位置 |
| 2 | 洛谷 P3375【模板】KMP | https://www.luogu.com.cn/problem/P3375 | 可用 Z 函数重写，输出所有匹配位置 |
| 3 | POJ 3461 Oulipo | http://poj.org/problem?id=3461 | 统计模式串出现次数，支持重叠匹配 |
| 4 | 力扣 686 重复叠加字符串匹配 | https://leetcode.cn/problems/repeated-string-match/ | 字符串拼接后匹配，可用 Z / KMP / Rabin-Karp |

## Z 函数做模式匹配

给定：

```text
pattern
text
```

构造：

```text
s = pattern + "#" + text
```

计算 `z` 数组。

如果在文本部分的某个位置 `i` 满足：

```text
z[i] >= pattern.size()
```

说明：

```text
pattern 在 text 中从对应位置开始出现。
```

代码示意：

```cpp
vector<int> findOccurrences(string text, string pattern) {
    string s = pattern + "#" + text;
    vector<int> z = z_function(s);

    vector<int> ans;
    int m = pattern.size();

    for (int i = m + 1; i < (int)s.size(); i++) {
        if (z[i] >= m) {
            ans.push_back(i - m - 1);
        }
    }

    return ans;
}
```

## 分隔符为什么重要？

如果不加分隔符，模式串和文本串的边界可能混在一起。

例如：

```text
pattern = aaa
text = aa
```

如果直接拼接成：

```text
aaaaa
```

Z 函数会把跨越边界的匹配也算进去，导致结果不可靠。

因此要使用：

```text
pattern + 不会出现的特殊字符 + text
```

## 本阶段常见错误

- 分隔符出现在原字符串中；
- 输出位置没有减去 `pattern.size() + 1`；
- 用 `z[i] == m` 而不是 `z[i] >= m`；
- 忘记重叠匹配是允许的；
- 模式串为空时没有单独处理；
- 文本串比模式串短时没有提前判断。

---

# 三、中等难度：border、周期、前后缀统计

## 学习目标

这一阶段用 Z 函数分析字符串结构。

重点是：

- 判断哪些前缀也是后缀；
- 求最长快乐前缀；
- 求所有 border；
- 统计 border 出现次数；
- 判断周期与循环节；
- 与 KMP 的 `pi` 数组对照理解。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | 力扣 1392 最长快乐前缀 | https://leetcode.cn/problems/longest-happy-prefix/ | 求最长相等真前缀和真后缀 |
| 2 | 力扣 459 重复的子字符串 | https://leetcode.cn/problems/repeated-substring-pattern/ | 判断字符串是否由某个子串重复构成 |
| 3 | 洛谷 P4391 [BOI2009] Radio Transmission 无线传输 | https://www.luogu.com.cn/problem/P4391 | 最小循环节，可用 KMP，也可用 Z 函数理解 |
| 4 | Codeforces 432D Prefixes and Suffixes | https://codeforces.com/problemset/problem/432/D | 输出所有既是前缀又是后缀的串长及出现次数 |
| 5 | Codeforces 126B Password | https://codeforces.com/problemset/problem/126/B | 找既是前缀又是后缀，且在中间出现过的最长串 |

## 如何用 Z 函数找 border？

一个长度为 `len` 的前缀，如果同时也是后缀，需要满足：

```text
z[n - len] == len
```

因为从位置 `n - len` 开始的后缀，长度正好是 `len`。  
如果它和原串前缀的 LCP 也是 `len`，说明这个前缀等于后缀。

例如：

```text
s = ababa
n = 5
```

长度为 3 的前缀：

```text
aba
```

也是后缀，因为：

```text
z[5 - 3] = z[2] = 3
```

## 如何统计前缀出现次数？

如果一个前缀长度为 `L`，它在字符串中出现一次，对应某个位置 `i` 满足：

```text
z[i] >= L
```

所以可以统计所有 `z[i]` 的值，然后做后缀累加：

```cpp
vector<int> freq(n + 1);

for (int i = 1; i < n; i++) {
    freq[z[i]]++;
}

for (int len = n - 1; len >= 1; len--) {
    freq[len] += freq[len + 1];
}
```

此时：

```text
freq[L] = 前缀长度 L 在除开位置 0 之外出现的次数
```

如果要算整个字符串中出现次数，还需要加上前缀自身的那一次：

```text
freq[L] + 1
```

## 如何用 Z 函数判断周期？

一种思路是枚举周期长度 `p`。

如果 `n % p == 0`，并且：

```text
z[p] >= n - p
```

说明从位置 `p` 开始的后缀与原串前缀匹配了 `n - p` 个字符，也就是：

```text
s[p...n-1] == s[0...n-p-1]
```

这说明字符串可以由长度为 `p` 的子串重复构成。

## 本阶段常见错误

- 找 border 时写成 `z[len] == len`；
- 统计出现次数时忘记 `z[i] >= L`，不是 `z[i] == L`；
- 忘记加上前缀自身出现的那一次；
- 判断周期时只看 `z[p]`，没有判断 `n % p == 0`；
- 允许真前缀时，不能把整个字符串本身算进去。

---

# 四、进阶应用：exKMP、LCP 查询、DP 与综合题

## 学习目标

这一阶段不只求单个字符串的 Z 数组，而是处理两个字符串之间的 LCP。

重点方向：

- exKMP；
- 两个字符串的后缀与模式串 LCP；
- 字符串得分统计；
- 字符串恢复；
- Z 函数 + DP；
- Z 函数与字符串哈希、KMP、后缀数组的对照。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | 洛谷 P5410【模板】扩展 KMP / exKMP（Z 函数） | https://www.luogu.com.cn/problem/P5410 | exKMP 模板：求模式串自身 Z 数组和文本串每个后缀的 LCP |
| 2 | 力扣 2223 构造字符串的总得分和 | https://leetcode.cn/problems/sum-of-scores-of-built-strings/ | 所有后缀与原串 LCP 总和 |
| 3 | 力扣 3031 将单词恢复初始状态所需的最短时间 II | https://leetcode.cn/problems/minimum-time-to-revert-word-to-initial-state-ii/ | 判断若干后缀是否等于原串前缀 |
| 4 | Codeforces 535D Tavas and Malekas | https://codeforces.com/problemset/problem/535/D | 多次放置模式串，Z 函数检查重叠一致性 |
| 5 | Codeforces 808G Anthem of Berland | https://codeforces.com/problemset/problem/808/G | KMP/Z 思想 + DP，带问号字符串匹配 |
| 6 | 洛谷 P5410 重写第二遍 | https://www.luogu.com.cn/problem/P5410 | 建议用两种写法实现：拼接法和标准 exKMP 法 |

## exKMP 求什么？

普通 Z 函数是：

```text
z[i] = LCP(s, s[i...])
```

exKMP 处理两个字符串：

```text
pattern = p
text = t
```

要求：

```text
extend[i] = LCP(p, t[i...])
```

也就是模式串 `p` 与文本串每个后缀的最长公共前缀长度。

这正是洛谷 P5410 要求的第二个数组。

## exKMP 与拼接法的关系

如果只想求：

```text
pattern 与 text 每个后缀的 LCP
```

可以构造：

```text
pattern + "#" + text
```

然后跑普通 Z 函数。

文本中第 `i` 个后缀对应拼接串中的位置：

```text
pattern.size() + 1 + i
```

所以：

```text
extend[i] = z[pattern.size() + 1 + i]
```

这种方法非常好理解。

标准 exKMP 写法则不一定需要真的拼接字符串，而是利用模式串自身的 Z 数组加速文本匹配。

## Z 函数 + DP

有些题不是直接问匹配位置，而是问：

```text
能否选择若干位置匹配模式串？
最多能匹配多少次？
拼接后是否合法？
删除/恢复后何时回到原串？
```

这时 Z 函数负责快速判断：

```text
某个位置开始能匹配模式串多少字符。
```

DP 或贪心负责整体决策。

## 本阶段常见错误

- exKMP 中模式串和文本串顺序写反；
- 拼接法中分隔符选择不安全；
- 洛谷 P5410 的输出是数组权值，不是直接输出数组；
- 两个字符串 LCP 结果需要截断到模式串长度；
- 重叠模式串一致性检查时没有比较重叠部分；
- DP 题中只会求匹配长度，不会设计状态。

---

# 推荐完整刷题路线

建议按照下面顺序推进：

```text
OI-Wiki Z 函数样例
→ P5410
→ LeetCode 2223
→ LeetCode 28
→ P3375
→ LeetCode 1392
→ LeetCode 459
→ P4391
→ CF432D
→ CF126B
→ LeetCode 3031
→ CF535D
→ CF808G
```

如果时间有限，可以只刷核心路线：

```text
P5410
→ LeetCode 2223
→ LeetCode 1392
→ LeetCode 459
→ CF432D
→ LeetCode 3031
```

如果只想掌握模板和基本应用：

```text
P5410
→ LeetCode 2223
→ LeetCode 28
→ LeetCode 1392
```

---

# 每道题的复盘模板

建议每做完一道 Z 函数题，都写下面几个问题。

## 1. 本题中 `z[i]` 表示什么？

例如：

```text
z[i] 表示从 i 开始的后缀与整个字符串的最长公共前缀长度。
```

或者：

```text
拼接串 pattern + '#' + text 中，文本部分的 z 值表示 pattern 与 text 后缀的 LCP。
```

## 2. 是否需要拼接字符串？

如果是模式匹配，通常写：

```text
pattern + '#' + text
```

如果只是分析单个字符串，直接对原串求 Z 数组即可。

## 3. 分隔符是否安全？

分隔符不能出现在原字符串中。

常见选择：

```text
'#'
'$'
'@'
```

但如果原串可能包含任意字符，就要更谨慎，或改用标准 exKMP 写法。

## 4. 查询的是等于、至少，还是最长？

例如：

```text
匹配模式串：z[i] >= pattern.size()
```

```text
找 border：z[n - len] == len
```

```text
统计前缀出现次数：统计 z[i] >= len 的位置数量
```

## 5. 是否涉及周期？

如果用 Z 函数判断周期，通常枚举周期长度 `p`，并检查：

```text
n % p == 0
z[p] >= n - p
```

## 6. 是否需要统计出现次数？

如果统计某个前缀出现次数，要注意：

```text
统计 z[i] >= len，而不是 z[i] == len。
```

---

# Z 函数与其他字符串算法的选择

## 更适合 Z 函数的情况

- 需要知道每个后缀和整串前缀的 LCP；
- 需要统计字符串所有后缀得分；
- 需要快速判断某个后缀是否等于前缀；
- 需要处理 pattern 与 text 每个后缀的 LCP；
- 需要比 KMP 更直观地观察“从每个位置开始能匹配多长”。

## 更适合 KMP 的情况

- 需要单模式串匹配；
- 习惯用 `next / pi` 数组；
- 需要构造 KMP 自动机；
- 需要分析每个前缀内部的最长 border。

## 更适合字符串哈希的情况

- 需要多次比较任意两个子串；
- 需要 LCP 二分；
- 可以接受低概率哈希冲突；
- 题目更偏工程实现和快速子串比较。

## 更适合后缀数组 / 后缀自动机的情况

- 需要处理任意后缀之间的 LCP；
- 需要统计不同子串数量；
- 需要复杂子串查询；
- 单纯 Z 函数不够表达全局后缀关系。

---

# 三类 Z 函数问题的区别

## 1. 模板与匹配类

目标：

```text
求 Z 数组，或用 pattern + '#' + text 查找模式串出现位置。
```

代表题：

```text
P5410
LeetCode 28
P3375
POJ 3461
```

## 2. border 与周期类

目标：

```text
利用 z[n - len] == len 判断前缀是否为后缀；
利用 z[p] >= n - p 判断周期。
```

代表题：

```text
LeetCode 1392
LeetCode 459
P4391
CF432D
CF126B
```

## 3. LCP 统计与综合类

目标：

```text
统计所有后缀与原串的 LCP，或判断大量位置的匹配合法性。
```

代表题：

```text
LeetCode 2223
LeetCode 3031
CF535D
CF808G
```

---

# 最终掌握标准

学完 Z 函数后，看到一道题应该能快速判断：

1. 题目是否需要比较某个后缀和整个字符串前缀？
2. 是否可以构造 `pattern + '#' + text`？
3. `z[i]` 在当前题目中代表哪两个字符串片段的 LCP？
4. 匹配条件是 `z[i] >= m`，还是 `z[i] == len`？
5. 是否需要统计 `z[i] >= L` 的个数？
6. 是否涉及 border、周期或循环节？
7. 是否需要 exKMP 的两个字符串 LCP 数组？
8. 是否应该改用 KMP、哈希、后缀数组或 AC 自动机？

如果这些问题都能回答出来，说明你已经能把 Z 函数作为字符串结构分析工具，而不是只会背模板。
