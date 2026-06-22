## 力扣 1521：找到最接近目标值的函数值
#### 求数组中距离目标值最小的某区间与值，返回该区间与值

```cpp
class Solution {
public:
    /*
        LogTrick 入门题: 求区间与值满足某性质的最值
    */
    int closestToTarget(vector<int>& arr, int target) {
        vector<pair<int, long long>> cur;  // {value, count}
        int res = INT_MAX;

        auto add = [&] (vector<pair<int, long long>> &v, int value, int count) {
            if (!v.empty() && v.back().first == value)
                v.back().second += count;
            else
                v.push_back({value, count});
        };

        for (int x: arr) {
            vector<pair<int, long long>> nxt;
            
            add(nxt, x, 1);
            for (auto [value, count]: cur)
                add(nxt, value & x, count);
            
            cur = move(nxt);

            for (auto [value, count]: cur)
                res = min(res, abs(value - target));
        }

        return res;
    }
};
```
