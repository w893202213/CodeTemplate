## 力扣3097：或值至少为K的最短子数组

#### 涉及到子数组长度最短的要求, LogTrick 就要维护 {value, left_idx/right_idx}

```cpp
class Solution {
public:
    /*
        LogTrick 变题: 维护 {value, left_idx}
        left_idx 是 "相等元素中最靠右的左端点"
    */
    int minimumSubarrayLength(vector<int>& nums, int k) {
        vector<pair<int, int>> cur;
        int res = INT_MAX;

        auto add = [&] (vector<pair<int, int>> &v, int value, int left_idx) {
            if (!v.empty() && v.back().first == value) {
                // 什么都不做
            } else {
                v.push_back({value, left_idx});
            }
        };

        for (int i = 0; i < nums.size(); i ++ ) {
            int x = nums[i];
            vector<pair<int, int>> nxt;

            add(nxt, x, i);
            for (auto [value, left_idx]: cur)
                add(nxt, value | x, left_idx);
            
            cur = move(nxt);

            for (auto [value, left_idx]: cur)
                if (value >= k)
                    res = min(res, i - left_idx + 1);
        }

        return res == INT_MAX? -1: res;
    }
};
```
