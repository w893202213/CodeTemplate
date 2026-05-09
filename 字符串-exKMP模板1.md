“模式串 $b$ 与主串 $a$ 的每一个后缀的最长公共前缀”。这在算法竞赛中通常被称为 Extend 数组。

代码：

```cpp


vector<int> get_extend(string s, string t) {
    int n = s.size(), m = t.size();
    vector<int> z = get_z(t);
    // 在 ExKMP 逻辑中，通常需要定义 z[0] = m，表示 t 与自己从 0 开始的后缀 LCP 是全长
    z[0] = m; 

    vector<int> p(n, 0);
    for (int i = 0, l = 0, r = 0; i < n; i ++ ) {
        // i <= r 是为了防止在初始阶段或超出匹配范围时越界
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
