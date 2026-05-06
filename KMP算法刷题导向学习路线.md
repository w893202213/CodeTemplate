# KMP 算法刷题导向学习路线

> 目标：以刷题为导向，按照从易到难的 4 个梯度系统学习 KMP 算法。  
> 推荐主线：OI-Wiki 学前缀函数与 KMP，洛谷刷模板与周期题，AcWing 练基础模板，力扣中文补面试题。  
> 前置建议：掌握字符串下标、暴力匹配、数组、前缀/后缀、循环节、简单 DP。

---

## 0. 学习入口：建议先收藏

| 网站 | 链接 | 用法 |
|---|---|---|
| OI-Wiki：前缀函数与 KMP 算法 | https://oi-wiki.org/string/kmp/ | 中文资料，系统讲前缀函数、KMP、字符串周期、前缀出现次数等应用 |
| 洛谷 KMP 题目列表 | https://www.luogu.com.cn/problem/list?keyword=KMP | 中文题源，适合按 KMP 标签找题 |
| 洛谷 P3375【模板】KMP | https://www.luogu.com.cn/problem/P3375 | KMP 模板题，建议至少独立写 2~3 遍 |
| AcWing 831 KMP 字符串 | https://www.acwing.com/problem/content/833/ | AcWing 算法基础课中的 KMP 模板题 |
| 力扣 28 找出字符串中第一个匹配项的下标 | https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/ | 面试版 KMP 入门题 |
| 力扣字符串标签 | https://leetcode.cn/tag/string/ | 可补充字符串匹配、重复子串、最长前后缀等题型 |

---

# KMP 到底解决什么问题？

KMP 主要解决的是**字符串匹配**问题：

```text
给定文本串 text 和模式串 pattern，
问 pattern 在 text 中出现的位置。
```

暴力匹配在失配时通常会回退文本串指针，复杂度最坏可达：

```text
O(nm)
```

KMP 的核心是利用模式串自身的前缀后缀信息，使文本串指针不回退，整体复杂度变为：

```text
O(n + m)
```

KMP 的学习重点不是背代码，而是理解：

```text
失配时，模式串应该跳到哪里继续匹配？
```

这个“跳到哪里”，就是由 `next` 数组或前缀函数 `pi` 决定的。

---

# 一、基础入门：next 数组 / 前缀函数 + 模板匹配

## 学习目标

这一阶段只做最基础的 KMP 模板题。

必须掌握：

- 什么是前缀；
- 什么是后缀；
- 什么是 border；
- `next[i]` 或 `pi[i]` 的含义；
- 如何求 `next` 数组；
- 如何用 `next` 数组做字符串匹配；
- 匹配成功后如何继续寻找下一次出现。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | 力扣 28 找出字符串中第一个匹配项的下标 | https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/ | 最基础的子串匹配，适合理解 KMP 使用场景 |
| 2 | AcWing 831 KMP 字符串 | https://www.acwing.com/problem/content/833/ | 输出模式串在文本串中所有出现位置 |
| 3 | 洛谷 P3375【模板】KMP | https://www.luogu.com.cn/problem/P3375 | 输出所有匹配位置，并输出前缀函数 / next 数组 |
| 4 | POJ 3461 Oulipo | http://poj.org/problem?id=3461 | 多组字符串匹配，统计出现次数，英文题但很经典 |

## 前缀函数版本模板

这里使用更接近 OI-Wiki 和现代写法的 `pi` 数组。

```cpp
vector<int> prefix_function(const string& s) {
    int n = s.size();
    vector<int> pi(n);

    for (int i = 1; i < n; i++) {
        int j = pi[i - 1];

        while (j > 0 && s[i] != s[j]) {
            j = pi[j - 1];
        }

        if (s[i] == s[j]) {
            j++;
        }

        pi[i] = j;
    }

    return pi;
}
```

## KMP 匹配写法

```cpp
vector<int> kmp_search(const string& text, const string& pattern) {
    string s = pattern + "#" + text;
    vector<int> pi = prefix_function(s);

    vector<int> ans;
    int m = pattern.size();

    for (int i = m + 1; i < (int)s.size(); i++) {
        if (pi[i] == m) {
            ans.push_back(i - 2 * m);
        }
    }

    return ans;
}
```

## 本阶段必须理解

### 1. `pi[i]` 的含义

```text
pi[i] 表示 s[0...i] 这个前缀中，
最长的“真前缀 = 真后缀”的长度。
```

### 2. 为什么失配时跳 `pi[j - 1]`

如果当前已经匹配了 `j` 个字符，但下一个字符失配：

```text
pattern[0...j-1] 已经匹配成功
pattern[j] 失配
```

此时不需要从头开始，而是找：

```text
pattern[0...j-1] 的最长相等真前缀和真后缀
```

也就是：

```cpp
j = pi[j - 1];
```

## 本阶段常见错误

- 把 `pi[i]` 理解成“当前位置之前匹配了多少次”；
- 混用 0 下标版和 1 下标版 `next`；
- 匹配成功后没有继续跳转，导致漏掉重叠匹配；
- 输出位置时 0 下标和 1 下标混乱；
- 分隔符 `#` 出现在原字符串中，导致合并字符串法出错；
- 模式串为空时没有特殊处理。

---

# 二、简单应用：重复子串、字符串周期与循环节

## 学习目标

这一阶段重点学习 KMP 的第一个重要应用：

```text
用 next / pi 数组分析字符串周期。
```

需要掌握：

- 最小循环节；
- 字符串是否由某个子串重复构成；
- 周期长度；
- 前后缀关系；
- `n - pi[n - 1]` 的意义。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | 力扣 459 重复的子字符串 | https://leetcode.cn/problems/repeated-substring-pattern/ | 判断字符串是否由子串重复构成 |
| 2 | 力扣 1392 最长快乐前缀 | https://leetcode.cn/problems/longest-happy-prefix/ | 求最长相等真前缀和真后缀 |
| 3 | 洛谷 P4391 [BOI2009] Radio Transmission 无线传输 | https://www.luogu.com.cn/problem/P4391 | 最小循环节模板题 |
| 4 | UVA 10298 Power Strings | https://www.luogu.com.cn/problem/UVA10298 | 判断字符串由多少个最小循环节构成 |
| 5 | HDU 1358 Period | https://acm.hdu.edu.cn/showproblem.php?pid=1358 | 输出每个前缀的周期信息，英文题但经典 |

## 最小循环节公式

设字符串长度为：

```text
n
```

最长相等真前缀真后缀长度为：

```text
pi[n - 1]
```

则可能的最小循环节长度为：

```text
len = n - pi[n - 1]
```

如果：

```text
n % len == 0
```

说明整个字符串可以由长度为 `len` 的子串重复构成。

否则，整个字符串不存在完整循环节，最小表示只能是它自身。

## 例子

```text
s = ababab
n = 6
pi[n - 1] = 4
len = 6 - 4 = 2
```

因为：

```text
6 % 2 == 0
```

所以最小循环节是：

```text
ab
```

重复次数是：

```text
3
```

## 本阶段常见错误

- 只计算 `n - pi[n - 1]`，却忘记判断 `n % len == 0`；
- 把“最长公共前后缀”误认为“一定是循环节”；
- 没有区分“周期存在”和“完整重复构成”；
- 对每个前缀判断周期时，没有使用对应前缀的 `pi[i]`；
- 忽略字符串长度为 1 的边界情况。

---

# 三、中等难度：border 树、失配树、计数与删除匹配

## 学习目标

这一阶段开始深入理解 KMP 的结构性质。

重点概念：

- border 链；
- fail / next 树；
- 失配树；
- 统计每个前缀出现次数；
- 非重叠前后缀；
- 用 KMP 处理删除子串；
- KMP 与栈结合。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | Codeforces 126B Password | https://codeforces.com/problemset/problem/126/B | 找既是前缀又是后缀且在中间出现过的最长串 |
| 2 | 洛谷 P3435 [POI2006] OKR-Periods of Words | https://www.luogu.com.cn/problem/P3435 | border 链与周期关系 |
| 3 | 洛谷 P2375 [NOI2014] 动物园 | https://www.luogu.com.cn/problem/P2375 | 非重叠前后缀计数，KMP 进阶经典题 |
| 4 | 洛谷 P5829【模板】失配树 | https://www.luogu.com.cn/problem/P5829 | fail 树 / 失配树模板 |
| 5 | 洛谷 P4824 [USACO15FEB] Censoring S | https://www.luogu.com.cn/problem/P4824 | KMP + 栈，删除所有出现的模式串 |
| 6 | 洛谷 P6216 回文匹配 | https://www.luogu.com.cn/problem/P6216 | KMP / Manacher 综合理解，可后置 |

## border 链

如果一个字符串的最长 border 长度是：

```text
pi[n - 1]
```

那么它的下一个 border 是：

```text
pi[pi[n - 1] - 1]
```

继续跳下去，就得到一条 border 链：

```text
n -> pi[n - 1] -> pi[pi[n - 1] - 1] -> ...
```

## 失配树 / fail 树

对每个前缀长度 `i`，都有一条边：

```text
i -> pi[i - 1]
```

这些边形成一棵树，常称为：

```text
失配树 / fail 树
```

它可以用来处理：

- border 祖先关系；
- 前缀之间的包含关系；
- 两个前缀的最长公共 border；
- 前缀出现次数统计；
- 某些 KMP 进阶计数问题。

## KMP + 栈删除匹配

以 P4824 为例：

```text
给定文本串和模式串，删除文本中所有出现的模式串。
```

普通做法会因为反复删除导致复杂度很高。

可以使用：

```text
栈 + KMP 当前匹配长度
```

每读入一个字符，就记录当前字符压入后匹配到模式串的第几位。  
如果完整匹配，就弹出模式串长度个字符，同时恢复之前的匹配状态。

## 本阶段常见错误

- border 链跳转下标写错；
- `pi[x]` 和 `pi[x - 1]` 混用；
- 统计前缀出现次数时没有从长到短回传；
- 非重叠前后缀题中忘记限制 `2 * len <= 当前长度`；
- 删除子串后没有恢复 KMP 当前状态；
- fail 树题没有把“前缀长度”当作树上的节点。

---

# 四、进阶问题：KMP + DP、自动机、矩阵优化与 exKMP 衔接

## 学习目标

这一阶段 KMP 不再只是字符串匹配，而是作为状态转移工具使用。

重点方向：

- KMP 自动机；
- KMP + 动态规划；
- KMP + 矩阵快速幂；
- KMP + 字符串压缩；
- KMP 与 exKMP / Z 函数对比；
- KMP 向 AC 自动机过渡。

| 方向 | 题目/资料 | 链接 | 训练点 |
|---|---|---|---|
| KMP + DP | Codeforces 808G Anthem of Berland | https://codeforces.com/problemset/problem/808/G | 含 `?` 的字符串中最大化模式串出现次数 |
| KMP + 矩阵快速幂 | 洛谷 P3193 [HNOI2008] GT考试 | https://www.luogu.com.cn/problem/P3193 | KMP 自动机 + DP + 矩阵快速幂 |
| KMP + 压缩/模板 | 洛谷 P3426 [POI2005] SZA-Template | https://www.luogu.com.cn/problem/P3426 | border 性质 + DP，难度较高 |
| KMP + 区间 DP | UVA 11022 String Factoring | https://www.luogu.com.cn/problem/UVA11022 | 用 KMP 判断循环节，辅助区间 DP |
| 扩展 KMP / Z 函数模板 | 洛谷 P5410【模板】扩展 KMP / exKMP | https://www.luogu.com.cn/problem/P5410 | 与 KMP 思想相近，适合作为下一阶段 |
| AC 自动机模板 | 洛谷 P3808【模板】AC 自动机（简单版） | https://www.luogu.com.cn/problem/P3808 | 多模式串匹配，KMP 的自然延伸方向 |
| OI-Wiki：Z 函数 | https://oi-wiki.org/string/z-func/ | 学习 exKMP / Z 函数 |
| OI-Wiki：AC 自动机 | https://oi-wiki.org/string/ac-automaton/ | 学习多模式串匹配 |

## KMP 自动机

KMP 自动机可以理解为：

```text
当前已经匹配了模式串前 j 位，
读入一个新字符 c 后，下一状态应该是多少？
```

也就是预处理：

```text
go[j][c]
```

表示：

```text
当前匹配长度为 j，读入字符 c 后的新匹配长度。
```

这在 DP 中很常见。

例如：

```text
f[i][j] = 处理到第 i 个位置，目前匹配了模式串前 j 位时的方案数或最大值。
```

然后枚举下一个字符进行转移。

## KMP + 矩阵快速幂

如果字符串长度很大，普通 DP 的复杂度可能过高。

此时如果状态转移固定，可以把 KMP 自动机转成矩阵：

```text
状态 = 当前匹配了模式串前 j 位
转移 = 读入一个字符后跳到新状态
```

再用矩阵快速幂优化。

洛谷 P3193 就属于这个方向。

## KMP 与 Z 函数 / exKMP 的关系

KMP 的前缀函数 `pi[i]` 关注：

```text
s[0...i] 的最长相等真前缀和真后缀。
```

Z 函数 `z[i]` 关注：

```text
从位置 i 开始的后缀，与整个字符串的最长公共前缀。
```

二者都在利用字符串自身的重复信息，只是观察角度不同。

如果你已经学完 KMP，建议继续学习：

```text
Z 函数 / exKMP
```

## KMP 与 AC 自动机的关系

KMP 可以看作：

```text
单模式串匹配。
```

AC 自动机可以看作：

```text
多模式串匹配。
```

如果把 Trie 中只插入一个模式串，AC 自动机的 fail 指针思想与 KMP 的失配跳转非常接近。

所以学习路径可以是：

```text
KMP → Trie → AC 自动机
```

或者：

```text
Trie → KMP → AC 自动机
```

## 本阶段常见错误

- KMP 自动机中没有处理匹配到完整模式串后的回退；
- DP 状态中把“已经匹配长度”与“出现次数”混在一起；
- 矩阵快速幂中状态转移方向写反；
- exKMP 和 KMP 的数组含义混淆；
- AC 自动机中把单模式串 fail 跳转照搬到多模式串场景，没有使用 Trie 结构。

---

# 推荐完整刷题路线

建议按照下面顺序推进：

```text
LeetCode 28
→ AcWing 831
→ P3375
→ LeetCode 459
→ LeetCode 1392
→ P4391
→ UVA10298
→ HDU1358
→ CF126B
→ P3435
→ P2375
→ P5829
→ P4824
→ CF808G
→ P3193
→ P3426
→ P5410
→ P3808
```

如果时间有限，可以只刷核心路线：

```text
AcWing 831
→ P3375
→ P4391
→ UVA10298
→ CF126B
→ P2375
→ P4824
→ P3193
```

---

# 每道题的复盘模板

建议每做完一道 KMP 题，都写下面几个问题。

## 1. 本题用 KMP 解决什么？

例如：

```text
本题用 KMP 找模式串在文本串中的所有出现位置。
```

或者：

```text
本题用 KMP 的 next 数组求字符串最小循环节。
```

或者：

```text
本题用 fail 树统计 border 关系。
```

## 2. `pi[i]` / `next[i]` 在本题中表示什么？

例如：

```text
pi[i] 表示 s[0...i] 的最长相等真前缀和真后缀长度。
```

如果使用 1 下标写法，也要明确：

```text
next[i] 表示前 i 个字符构成的子串的最长相等真前缀和真后缀长度。
```

## 3. 是否涉及周期？

如果涉及，写出：

```text
len = n - pi[n - 1]
```

并判断：

```text
n % len == 0
```

## 4. 是否要统计 border 链？

如果要统计，就写出：

```text
i -> pi[i - 1] -> pi[pi[i - 1] - 1] -> ...
```

并说明是否需要建 fail 树。

## 5. 是否需要与 DP、栈或自动机结合？

例如：

```text
本题是 KMP + 栈，删除匹配串后要恢复之前的匹配状态。
```

或者：

```text
本题需要构造 KMP 自动机，把匹配长度作为 DP 状态。
```

---

# KMP 与其他字符串算法的选择

## 更适合 KMP 的情况

- 单模式串匹配；
- 需要所有出现位置；
- 需要处理重叠匹配；
- 需要分析前后缀；
- 需要求最小循环节；
- 需要构造 KMP 自动机；
- 需要在 DP 中维护“已匹配模式串前多少位”。

## 更适合字符串哈希的情况

- 需要快速比较大量子串是否相等；
- 多次查询任意子串；
- 可以接受极小概率哈希冲突；
- 题目对实现速度要求高，KMP 建模不直观。

## 更适合 Z 函数 / exKMP 的情况

- 需要每个后缀与整串的最长公共前缀；
- 需要比较模式串与文本串每个后缀的 LCP；
- 需要更直接地处理“从每个位置开始能匹配多长”。

## 更适合 AC 自动机的情况

- 多个模式串同时匹配；
- 统计多个关键词在长文本中的出现；
- 模式串集合较大；
- 需要 Trie + fail 指针。

---

# 三类 KMP 问题的区别

## 1. 模板匹配类

目标：

```text
找 pattern 在 text 中出现的位置或次数。
```

代表题：

```text
AcWing 831
P3375
LeetCode 28
POJ 3461
```

## 2. 周期循环节类

目标：

```text
用 next / pi 数组分析字符串是否由某个子串重复构成。
```

代表题：

```text
P4391
UVA10298
HDU1358
LeetCode 459
```

## 3. border / fail 树类

目标：

```text
深入利用前缀和后缀之间的层级关系。
```

代表题：

```text
CF126B
P3435
P2375
P5829
```

## 4. 自动机 / DP 类

目标：

```text
把“当前匹配到模式串第几位”作为状态。
```

代表题：

```text
CF808G
P3193
P3426
UVA11022
```

---

# 最终掌握标准

学完 KMP 后，看到一道题应该能快速判断：

1. 是否是单模式串匹配？
2. 是否需要所有匹配位置，还是只需要第一次出现？
3. 是否允许重叠匹配？
4. 是否需要分析前缀、后缀、border 或周期？
5. 是否要使用 `n - pi[n - 1]` 求循环节？
6. 是否需要沿着 border 链不断跳？
7. 是否需要建失配树 / fail 树？
8. 是否需要把 KMP 匹配长度作为 DP 状态？
9. 是否该换用 Z 函数、字符串哈希或 AC 自动机？

如果这些问题都能回答出来，说明你已经不是只会背 KMP 模板，而是能真正用 KMP 分析字符串结构。
