#### 最小循环节模板题：力扣459
#### 询问输入的字符串是否是由某个字符串循环多次得到的

代码：

```cpp
class Solution {
public:
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

    /*
        关键判断：
        1. 必须有公共前后缀 (pi[n-1] > 0)
        2. 最小周期长度 n - pi[n-1] 必须能整除 n

        pi数组的数学性质保证了：如果上述两个条件满足，前面的部分一定是通过该循环节重复得到的。
    */
    bool repeatedSubstringPattern(string s) {
        int n = s.size();
        if (n <= 1)  return false;
        vector<int> pi = prefix_function(s);
        int len = n - pi[n - 1];  // 最小循环节长度
        return (pi[n - 1] > 0) && (n % len == 0);
    }
};
```
