#### 核心是 prefix_function(计算 pi 数组) 和 kmp(计算 t 在 s 中出现的所有位置的下标)
#### Acwing 831

代码：

```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

vector<int> prefix_function(string s) {
    int n = s.size();
    vector<int> pi(n, 0);
    for (int i = 1; i < n; i ++ ) {
        int j = pi[i - 1];
        while (j > 0 && s[i] != s[j])  j = pi[j - 1];
        if (s[i] == s[j])  j ++;
        pi[i] = j;
    }
    return pi;
}

vector<int> kmp(string s, string t) {
    string str = t + "#" + s;
    vector<int> pi = prefix_function(str);
    vector<int> res;
    for (int i = t.size() + 1; i <= s.size() + t.size(); i ++ )
        if (pi[i] == t.size())
            res.push_back(i - 2 * t.size());
    return res;
}

int main() {
    int n, m;
    string s, t;
    
    cin >> m >> t >> n >> s;
    
    vector<int> res = kmp(s, t);
    for (int &x: res)
        cout << x << " ";
    
    return 0;
}
```
