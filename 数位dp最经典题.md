### 读入两个数 a、b，输出两数之间每个数位出现的次数

代码：

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>

using namespace std;

string s;
int b;
int memo[10][10];

// cnt 是之前遍历到的位置中数位 b 出现的次数
long long dfs(int pos, int cnt, bool is_limit, bool is_num) {
    if (pos == s.size())
        return is_num? cnt: 0;
    
    if (is_limit == false && is_num && memo[pos][cnt] != -1)
        return memo[pos][cnt];
    
    long long res = 0;
    if (is_num == false)
        res += dfs(pos + 1, 0, false, false);
    
    int up = is_limit? (s[pos] - '0'): 9;
    int down = is_num? 0: 1;
    for (int d = down; d <= up; d ++ )
        res += dfs(pos + 1, cnt + (d == b), is_limit && (d == up), true);
    
    if (is_limit == false && is_num)
        memo[pos][cnt] = res;
    
    return res;
}

// 返回 0~n 中数字 b 的出现次数
long long get(int n) {
    s = to_string(n);
    memset(memo, -1, sizeof memo);
    return dfs(0, 0, true, false);
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);
    
    int A, B;
    while (cin >> A >> B) {
        if (A == 0 && B == 0)
            break;
        
        if (A > B)
            swap(A, B);
        
        for (int i = 0; i <= 9; i ++ ) {
            b = i;
            cout << get(B) - get(A - 1) << " ";
        }
        cout << endl;
    }
    
    return 0;
}
```
