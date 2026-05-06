## 阿里研发岗2026-3-25第3题

题目描述
定义一个正整数是”喜欢的”，当且仅当它同时满足以下两个条件：

1. 不能被 3 整除
2. 十进制表示中不包含数字 ‘3’

给定 k，求第 k 个”喜欢的”正整数。

样例

输入

5
1
2
3
4
5

输出

1
2
4
5
7

#### 本题是经典的”求第 k 个满足条件的数”问题，标准解法是 二分答案 + 数位DP 计数。

代码：
```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>

using namespace std;

typedef long long LL;

string s;
LL memo[32][3];

// prefix 表示之前的数位之和模 3 的余数
LL dfs(int pos, int prefix, bool is_limit, bool is_num) {
    if (pos == s.size())
        return is_num && prefix != 0;
    
    if (is_limit == false && is_num && memo[pos][prefix] != -1)
        return memo[pos][prefix];
    
    long long res = 0;
    if (is_num == false)
        res += dfs(pos + 1, prefix, false, false);
    
    int up = is_limit? (s[pos] - '0'): 9;
    int down = is_num? 0: 1;
    for (int d = down; d <= up; d ++ )
        if (d != 3)
            res += dfs(pos + 1, (prefix + d) % 3, is_limit && (d == up), true);
    
    if (is_limit == false && is_num)
        memo[pos][prefix] = res;
    
    return res;
}

// 返回 1~n 中有多少个"喜欢的数"
LL get(LL n) {
    s = to_string(n);
    memset(memo, -1, sizeof memo);
    return dfs(0, 0, true, false);
}

int main() {
    
    int n;
    cin >> n;
    
    LL k;
    while (n -- ) {
        cin >> k;
        
        LL l = 1, r = 2e18;
        while (l < r) {
            LL mid = l + r >> 1;
            if (get(mid) >= k)  r = mid;
            else  l = mid + 1;
        }
        
        cout << r << endl;
    }
    
    return 0;
}
```
