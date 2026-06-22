## 力扣2447：最大公因数GCD等于K的子数组数目

```cpp
class Solution {
public:
    /*
        LogTrcik 入门题: 计数版 LogTrick 
    */
    int subarrayGCD(vector<int>& nums, int k) {
        vector<pair<int, long long>> cur;
        int res = 0;

        auto add = [&] (vector<pair<int, long long>> &v, int value, long long count) {
            if (!v.empty() && v.back().first == value)
                v.back().second += count;
            else
                v.push_back({value, count});
        };

        for (int x: nums) {
            vector<pair<int, long long>> nxt;

            add(nxt, x, 1);
            for (auto [value, count]: cur)
                add(nxt, __gcd(value, x), count);
            
            cur = move(nxt);

            for (auto [value, count]: cur)
                if (value == k)
                    res += count;
        }

        return res;
    }
};
```
