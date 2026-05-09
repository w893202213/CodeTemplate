“模式串 $b$ 与主串 $a$ 的每一个后缀的最长公共前缀”。这在算法竞赛中通常被称为 Extend 数组。

代码：

```cpp
vector<int> get_z(string s) {
    int n = s.size();
    vector<int> z(n, 0);
    for (int i = 1, l = 0, r = 0; i < n; i ++ ) {
        if (z[i - l] < r - i + 1) {
            z[i] = z[i - l];
        } else {
            z[i] = max(r - i + 1, 0);
            while (i + z[i] < n && s[z[i]] == s[i + z[i]])
                z[i] ++;
            l = i, r = i + z[i] - 1;
        }
    }
    return z;
}

vector<int> get_extend(string s, string t) {
    int n = s.size(), m = t.size();
    vector<int> z = get_z(t);
    vector<int> p(n, 0);
    for (int i = 0, l = 0, r = 0; i < n; i ++ ) {
        if (i <= r && z[i - l] < r - i + 1) {
            p[i] = z[i - l];
        } else {
            p[i] = max(0, r - i + 1);
            while (i + p[i] < n && p[i] < m && s[i + p[i]] == t[p[i]])
                p[i] ++;
            l = i, r = i + p[i] - 1;
        }
    }
    return p;
}
```
