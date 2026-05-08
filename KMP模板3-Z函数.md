#### 理论推导：https://zhuanlan.zhihu.com/p/403256847

#### 纯模板：力扣 2223 
#### 求所有 Z 函数值的和

代码：

```cpp
class Solution {
public:
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
    
    // z 函数的模板题
    long long sumScores(string s) {
        vector<int> z = get_z(s);
        long long res = s.size();  // 由于 z[0] = 0 因此需要补上原串的长度
        for (int x: z)
            res += x;
        return res;
    }
};
```


#### 实现字符串匹配（KMP的经典功能）

代码：

```cpp
class Solution {
public:
    vector<int> calc_z(string s) {
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

    vector<int> match(string s, string t) {
        string str = t + "#" + s;
        vector<int> z = calc_z(str);
        vector<int> res;
        for (int i = t.size() + 1; i < z.size(); i++) {
            if (z[i] == t.size()) {
                res.push_back(i - (t.size() + 1));
            }
        }
        return res;
    }

    int strStr(string haystack, string needle) {
        vector<int> pos = match(haystack, needle);
        if (pos.size())
            return pos[0];
        else
            return -1;
    }
};
```
