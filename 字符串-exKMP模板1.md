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
    string str = t + "#" + s;
    vector<int> z = get_z(str);
    // 截取后面关于 s 的部分即可
    return vector<int>(z.begin() + t.size() + 1, z.end());
}
```
