# AC 自动机刷题导向学习路线

> 目标：以刷题为导向，按照从易到难的 4 个梯度系统学习 AC 自动机。  
> 推荐主线：OI-Wiki 学原理，洛谷刷模板与进阶题，力扣中文补 Trie/KMP 前置题。  
> 前置建议：已经掌握 Trie 树、KMP 的失配思想、BFS、队列、动态规划、树状数组基础。

---

## 0. 学习入口：建议先收藏

| 网站 | 链接 | 用法 |
|---|---|---|
| OI-Wiki：AC 自动机 | https://oi-wiki.org/string/ac-automaton/ | 中文资料，系统学习 Trie 建树、fail 指针、自动机转移、匹配流程 |
| 洛谷 AC 自动机题单 | https://www.luogu.com.cn/training/60465 | 中文题单，题目按模板、进阶、较难方向分类 |
| 洛谷 P3808【模板】AC 自动机（简单版） | https://www.luogu.com.cn/problem/P3808 | 入门模板，统计有多少不同模式串在文本中出现 |
| 洛谷 P3796【模板】AC 自动机（加强版） | https://www.luogu.com.cn/problem/P3796 | 统计每个模式串出现次数，并输出出现次数最多的模式串 |
| 洛谷 P5357【模板】AC 自动机（二次加强版） | https://www.luogu.com.cn/problem/P5357 | 统计每个模式串在文本中出现次数，练 fail 树 / 拓扑统计 |
| 力扣 Trie 标签 | https://leetcode.cn/tag/trie/ | 可作为 Trie 前置训练 |
| 力扣 208 实现 Trie | https://leetcode.cn/problems/implement-trie-prefix-tree/ | AC 自动机前置：先会写 Trie |
| 力扣 28 找出字符串中第一个匹配项的下标 | https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/ | KMP 前置：理解单模式串失配跳转 |

---

# AC 自动机到底解决什么问题？

AC 自动机主要解决的是**多模式串匹配**问题。

普通 KMP 适合：

```text
一个模式串 pattern 匹配一个文本串 text。
```

AC 自动机适合：

```text
多个模式串 patterns 同时匹配一个文本串 text。
```

例如给定模式串：

```text
he
she
his
hers
```

再给定文本串：

```text
ushers
```

AC 自动机可以一次扫描文本串，就找出所有出现过的模式串。

可以把 AC 自动机理解成：

```text
Trie + KMP 的 fail 思想
```

其中：

```text
Trie：存储所有模式串；
fail 指针：失配时跳到最长可匹配后缀；
自动机转移：保证扫描文本串时每个字符只处理一次。
```

---

# 一、基础入门：Trie + fail 指针 + 模板匹配

## 学习目标

这一阶段只需要掌握最基础的 AC 自动机：

- 把所有模式串插入 Trie；
- 用 BFS 建立 fail 指针；
- 用文本串在自动机上跑匹配；
- 统计哪些模式串出现过；
- 理解“失配跳转”和“补全转移”的区别。

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | 力扣 208 实现 Trie 前缀树 | https://leetcode.cn/problems/implement-trie-prefix-tree/ | 前置题，先会写 Trie |
| 2 | 力扣 28 找出字符串中第一个匹配项的下标 | https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/ | 前置题，理解 KMP 失配跳转 |
| 3 | 洛谷 P3808【模板】AC 自动机（简单版） | https://www.luogu.com.cn/problem/P3808 | AC 自动机入门模板，统计出现过的不同模式串数量 |
| 4 | 洛谷 P3796【模板】AC 自动机（加强版） | https://www.luogu.com.cn/problem/P3796 | 统计每个模式串出现次数，输出出现最多的模式串 |
| 5 | HDU 2222 Keywords Search | https://acm.hdu.edu.cn/showproblem.php?pid=2222 | 英文经典模板题，可作为补充练习 |

## 基础流程

AC 自动机通常分三步：

```text
1. insert：把所有模式串插入 Trie；
2. build：BFS 建 fail 指针，并补全转移；
3. query：用文本串在自动机上匹配。
```

## 基础模板：数组版 AC 自动机

```cpp
const int N = 1000000 + 10;

int tr[N][26];
int fail[N];
int cnt[N];
int idx = 0;

void insert(const string& s) {
    int p = 0;
    for (char ch : s) {
        int c = ch - 'a';
        if (!tr[p][c]) {
            tr[p][c] = ++idx;
        }
        p = tr[p][c];
    }
    cnt[p]++;
}

void build() {
    queue<int> q;

    for (int c = 0; c < 26; c++) {
        if (tr[0][c]) {
            q.push(tr[0][c]);
        }
    }

    while (!q.empty()) {
        int u = q.front();
        q.pop();

        for (int c = 0; c < 26; c++) {
            int v = tr[u][c];

            if (v) {
                fail[v] = tr[fail[u]][c];
                q.push(v);
            } else {
                tr[u][c] = tr[fail[u]][c];
            }
        }
    }
}
```

## 查询模板：简单计数版

```cpp
int query(const string& text) {
    int p = 0;
    int ans = 0;

    for (char ch : text) {
        int c = ch - 'a';
        p = tr[p][c];

        for (int j = p; j && cnt[j] != -1; j = fail[j]) {
            ans += cnt[j];
            cnt[j] = -1; // 防止同一个模式串被重复统计
        }
    }

    return ans;
}
```

## 本阶段必须理解

### 1. fail 指针指向什么？

```text
fail[u] 指向当前节点 u 所代表字符串的最长真后缀，
并且这个后缀也必须是 Trie 中某个节点代表的字符串。
```

简单说：

```text
匹配失败时，不从头开始，而是跳到还能继续匹配的最长后缀位置。
```

### 2. 为什么用 BFS 建 fail？

因为一个节点的 fail 指针依赖它父节点的 fail 指针。

所以必须按层处理：

```text
先处理浅层节点，再处理深层节点。
```

### 3. 补全转移有什么作用？

补全转移后：

```cpp
tr[u][c]
```

一定表示从状态 `u` 读入字符 `c` 后应该到达的状态。

这样查询时就不需要反复 while 跳 fail，效率更稳定。

## 本阶段常见错误

- 只建 Trie，没有建 fail 指针；
- fail 指针建错，尤其是根节点子节点；
- 查询时忘记沿 fail 链统计模式串结尾；
- 同一个模式串重复出现时，题目要求“出现过”还是“出现次数”没有分清；
- 多组测试数据没有清空数组；
- 字符集不是小写字母，却仍然使用 `26`；
- 补全转移后，错误地把 `tr[u][c]` 当作原始 Trie 边。

---

# 二、简单应用：出现次数统计、fail 链统计与 fail 树

## 学习目标

这一阶段重点学习 AC 自动机的统计方式。

基础模板题一般只统计：

```text
有多少模式串出现过。
```

但很多题需要统计：

```text
每个模式串出现了多少次。
```

此时常用做法有两类：

```text
1. 查询文本时沿 fail 链统计；
2. 先记录每个状态被访问次数，再沿 fail 树从深到浅汇总。
```

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | 洛谷 P5357【模板】AC 自动机（二次加强版） | https://www.luogu.com.cn/problem/P5357 | 统计每个模式串出现次数，fail 树 / 拓扑汇总 |
| 2 | 洛谷 P3966 [TJOI2013] 单词 | https://www.luogu.com.cn/problem/P3966 | 多个单词互相作为文本，统计每个单词出现次数 |
| 3 | 洛谷 P3121 [USACO15FEB] Censoring G | https://www.luogu.com.cn/problem/P3121 | 多模式串删除，AC 自动机 + 栈 |
| 4 | 洛谷 P2292 [HNOI2004] L 语言 | https://www.luogu.com.cn/problem/P2292 | 字典匹配 + DP，可先用 Trie，后续可与 AC 自动机思想衔接 |
| 5 | UVA 11019 Matrix Matcher | https://www.luogu.com.cn/problem/UVA11019 | 二维模式匹配，可作为扩展题 |

## fail 树是什么？

如果把每个节点 `u` 连到 `fail[u]`，就会得到一棵树，通常称为：

```text
fail 树
```

或者：

```text
失配树
```

在 fail 树上，一个节点的祖先表示：

```text
当前字符串的某个后缀模式。
```

因此，如果某个状态在文本扫描中出现了 `x` 次，那么它的 fail 祖先对应的模式串也会出现至少 `x` 次。

## 出现次数统计的常见做法

### 做法一：查询时跳 fail 链

适合数据较小的题。

```cpp
for (int j = p; j; j = fail[j]) {
    ans[j]++;
}
```

问题是：  
如果 fail 链很长，复杂度可能退化。

### 做法二：拓扑 / fail 树汇总

适合数据较大的题。

步骤：

```text
1. 扫描文本串，记录每个自动机状态被访问了多少次；
2. 按 BFS 序的逆序处理节点；
3. 把子节点出现次数累加到 fail 父节点；
4. 每个模式串结尾节点的计数就是该模式串出现次数。
```

伪代码：

```cpp
for (int i = order.size() - 1; i >= 0; i--) {
    int u = order[i];
    vis[fail[u]] += vis[u];
}
```

## AC 自动机 + 栈删除

P3121 这类题类似 KMP 删除子串，但有多个模式串。

做法：

```text
1. 用 AC 自动机扫描文本；
2. 栈中保存字符和当前自动机状态；
3. 如果当前状态对应某个模式串结尾，就弹出该模式串长度；
4. 弹出后恢复栈顶保存的自动机状态。
```

## 本阶段常见错误

- 统计每个模式串出现次数时，只统计了结尾节点，没把 fail 子树贡献汇总上来；
- 反向拓扑顺序写错；
- 多个相同模式串没有保存多个编号；
- 模式串是另一个模式串的后缀时漏计；
- 删除题中弹栈后没有恢复自动机状态；
- `cnt[u]` 既表示结尾数量又表示访问次数，变量含义混乱。

---

# 三、中等难度：AC 自动机 + DP

## 学习目标

这一阶段学习 AC 自动机最常见的中等应用：

```text
在自动机上做 DP。
```

这类题的本质是：

```text
自动机节点 = 当前已经匹配到哪些模式串后缀状态；
DP = 在这些状态上计数、求最大值或判断可行性。
```

常见状态：

```text
f[i][u] = 长度为 i，当前位于自动机节点 u 的方案数 / 最大得分 / 是否可行。
```

| 推荐顺序 | 题目 | 链接 | 训练点 |
|---|---|---|---|
| 1 | 洛谷 P4052 [JSOI2007] 文本生成器 | https://www.luogu.com.cn/problem/P4052 | AC 自动机 + 计数 DP，避免出现敏感词 |
| 2 | 洛谷 P3041 [USACO12JAN] Video Game G | https://www.luogu.com.cn/problem/P3041 | AC 自动机 + DP，最大化匹配得分 |
| 3 | 洛谷 P3311 [SDOI2014] 数数 | https://www.luogu.com.cn/problem/P3311 | 数位 DP + AC 自动机，不能包含禁用串 |
| 4 | 洛谷 P2444 [POI2000] 病毒 | https://www.luogu.com.cn/problem/P2444 | AC 自动机 + 图上判环，判断是否存在无限安全串 |
| 5 | Codeforces 86C Genetic engineering | https://codeforces.com/problemset/problem/86/C | AC 自动机 + DP，英文进阶题 |

## AC 自动机 DP 的基本套路

### 1. 标记危险节点

如果某个节点是模式串结尾，或它的 fail 祖先是模式串结尾，那么这个节点通常也是危险节点。

可以在 BFS 建 fail 时顺便处理：

```cpp
danger[u] |= danger[fail[u]];
```

### 2. 在自动机上转移

典型 DP：

```cpp
f[0][0] = 1;

for (int i = 0; i < n; i++) {
    for (int u = 0; u <= idx; u++) {
        for (int c = 0; c < 26; c++) {
            int v = tr[u][c];

            if (!danger[v]) {
                f[i + 1][v] += f[i][u];
            }
        }
    }
}
```

### 3. 最后汇总答案

如果题目要求不包含任何模式串：

```text
答案 = 所有非危险状态的方案数之和。
```

如果题目要求至少包含一个模式串，常见做法是：

```text
总方案数 - 不包含任何模式串的方案数。
```

## P2444 病毒：图上判环

这道题不是普通计数 DP。

思路是：

```text
如果自动机中存在一个由非危险节点组成的可达环，
那么就能构造无限长且不包含病毒串的文本。
```

因此可以：

```text
1. 建 AC 自动机；
2. 标记危险节点；
3. 在非危险状态图中 DFS；
4. 判断是否存在环。
```

## 本阶段常见错误

- 没有把 fail 祖先的危险信息传递下来；
- DP 转移时走到了危险节点；
- 只判断节点本身是否为单词结尾，没有判断 fail 链上是否有单词结尾；
- 计数 DP 中忘记取模；
- 数位 DP 里没有处理前导零和上界限制；
- 判环题中把 Trie 边和补全后的自动机边混淆。

---

# 四、进阶问题：AC 自动机 + 数据结构 / 矩阵优化 / 动态维护

## 学习目标

这一阶段 AC 自动机会和其他算法结合，难度明显提高。

重点方向：

- fail 树 + DFS 序；
- fail 树 + 树状数组；
- fail 树 + 线段树；
- AC 自动机 + 矩阵快速幂；
- 动态 AC 自动机；
- AC 自动机 + 树剖；
- 多文本、多询问离线处理。

| 方向 | 题目/资料 | 链接 | 训练点 |
|---|---|---|---|
| fail 树 + 树状数组 | 洛谷 P2414 [NOI2011] 阿狸的打字机 | https://www.luogu.com.cn/problem/P2414 | AC 自动机 + fail 树 DFS 序 + 树状数组 |
| 多模式串查询 | CF1202E You Are Given Some Strings... | https://www.luogu.com.cn/problem/CF1202E | 正反 AC 自动机，统计前后缀拼接贡献 |
| 动态启用/禁用模式串 | CF163E e-Government | https://www.luogu.com.cn/problem/CF163E | AC 自动机 + fail 树 + 树状数组 |
| 动态字符串集合 | CF710F String Set Queries | https://www.luogu.com.cn/problem/CF710F | 动态 AC 自动机思想，分块 / 二进制合并 |
| 矩阵快速幂优化 DP | SP1676 GEN - Text Generator | https://www.luogu.com.cn/problem/SP1676 | AC 自动机 + DP + 矩阵快速幂 |
| 矩阵快速幂优化最大值 | CF696D Legen... | https://www.luogu.com.cn/problem/CF696D | AC 自动机 + 矩阵快速幂 + max-plus 矩阵 |
| 较难综合 | 洛谷 P5840 [COCI2015] Divljak | https://www.luogu.com.cn/problem/P5840 | AC 自动机 + fail 树 + 树状数组，较难 |

## fail 树 + DFS 序

在 fail 树中：

```text
一个节点的子树，表示所有以该节点字符串为后缀的自动机状态。
```

因此，如果要查询某个模式串在当前文本路径中出现了多少次，可以转成：

```text
查询 fail 树上某个节点子树内有多少个被激活状态。
```

这就可以使用：

```text
DFS 序 + 树状数组
```

或：

```text
DFS 序 + 线段树
```

典型题是 P2414 阿狸的打字机。

## 动态模式串集合

普通 AC 自动机适合：

```text
模式串集合固定。
```

如果题目要求动态插入、删除或启用禁用模式串，就需要结合其他技巧。

常见方法：

```text
1. 对固定集合建一次 AC 自动机，再用 fail 树 + 树状数组维护启用状态；
2. 对动态插入集合使用二进制分组，维护多个 AC 自动机；
3. 对删除操作使用正负贡献或重建。
```

## AC 自动机 + 矩阵快速幂

如果题目要求构造长度很大的字符串，并统计模式串出现次数或最大得分，普通 DP 可能太慢。

可以把自动机转移看成矩阵：

```text
状态 = 自动机节点
边 = 读入某个字符后的转移
权值 = 匹配到的模式串贡献
```

然后用矩阵快速幂优化。

## 本阶段常见错误

- fail 树 DFS 序中没有正确维护子树区间；
- 查询“某模式串出现次数”时，应该查它结尾节点的 fail 子树，而不是只查结尾节点；
- 动态启用/禁用模式串时没有处理重复操作；
- 多个 AC 自动机合并时复杂度失控；
- 矩阵快速幂中使用普通加乘，但题目其实需要 max-plus 矩阵；
- 字符串很长时仍然用普通 DP，导致超时。

---

# 推荐完整刷题路线

建议按照下面顺序推进：

```text
LeetCode 208
→ LeetCode 28
→ P3808
→ P3796
→ P5357
→ P3966
→ P3121
→ P4052
→ P3041
→ P3311
→ P2444
→ P2414
→ CF1202E
→ CF163E
→ SP1676
→ CF696D
→ P5840
→ CF710F
```

如果时间有限，可以只刷核心路线：

```text
P3808
→ P3796
→ P5357
→ P3966
→ P4052
→ P3041
→ P2444
→ P2414
```

---

# 每道题的复盘模板

建议每做完一道 AC 自动机题，都写下面几个问题。

## 1. 本题是多模式串匹配吗？

例如：

```text
本题给出多个模式串和一个文本串，需要一次扫描文本统计所有模式串出现情况，因此适合 AC 自动机。
```

## 2. 模式串集合是否固定？

例如：

```text
本题模式串全部预先给出，可以建一次 AC 自动机。
```

或者：

```text
本题有动态启用/禁用模式串，需要在 fail 树上维护状态。
```

## 3. 需要统计什么？

常见答案：

```text
统计有多少个不同模式串出现过。
```

```text
统计每个模式串出现次数。
```

```text
统计不包含任何模式串的字符串方案数。
```

```text
统计最大匹配得分。
```

## 4. 是否需要沿 fail 链或 fail 树统计？

如果只判断出现过，可以直接在匹配时处理。

如果要统计每个模式串出现次数，通常需要：

```text
访问次数 + fail 树反向汇总。
```

或者：

```text
fail 树 DFS 序 + 树状数组。
```

## 5. 是否需要 DP？

如果题目出现：

```text
构造长度为 n 的字符串
不能包含某些单词
最大化得分
统计方案数
```

通常就是：

```text
AC 自动机 + DP
```

---

# AC 自动机与 Trie、KMP 的关系

## Trie

Trie 解决：

```text
多个字符串的存储和前缀查询。
```

但普通 Trie 只能从根开始匹配，不能高效处理文本串中的任意位置匹配。

## KMP

KMP 解决：

```text
一个模式串在文本串中的匹配。
```

它用 `next / pi` 数组处理单模式串失配跳转。

## AC 自动机

AC 自动机解决：

```text
多个模式串在文本串中的匹配。
```

它相当于把 KMP 的失配思想扩展到 Trie 上。

一句话总结：

```text
Trie 管多个模式串的前缀结构；
KMP 管单模式串的失配跳转；
AC 自动机 = Trie + 多模式串失配跳转。
```

---

# 三类 AC 自动机问题的区别

## 1. 模板匹配类

目标：

```text
给多个模式串和一个文本串，统计模式串出现情况。
```

代表题：

```text
P3808
P3796
P5357
HDU2222
```

## 2. 出现次数统计类

目标：

```text
统计每个模式串出现多少次，或统计单词之间的包含关系。
```

代表题：

```text
P5357
P3966
P3121
```

## 3. 自动机 DP 类

目标：

```text
在自动机上构造字符串、计数、求最大值或判断可行性。
```

代表题：

```text
P4052
P3041
P3311
P2444
```

## 4. fail 树 + 数据结构类

目标：

```text
把 fail 关系转成树上子树问题，再用树状数组、线段树等维护。
```

代表题：

```text
P2414
CF163E
P5840
CF710F
```

---

# 最终掌握标准

学完 AC 自动机后，看到一道题应该能快速判断：

1. 是否是多模式串匹配？
2. 是否能先把所有模式串插入 Trie？
3. 是否需要 fail 指针？
4. 查询时是统计出现过，还是统计出现次数？
5. 是否需要 fail 树汇总贡献？
6. 是否需要把危险状态通过 fail 指针向下传递？
7. 是否是 AC 自动机 + DP？
8. 是否需要 fail 树 DFS 序 + 树状数组？
9. 模式串集合是否动态变化？
10. 是否需要矩阵快速幂优化自动机 DP？

如果这些问题都能回答出来，说明你已经不只是会写模板，而是能把 AC 自动机作为“多模式串匹配状态机”来建模。
