## 力扣 898：子数组按位异或操作
#### 给定一个整数数组 arr，返回所有 arr 的非空子数组的不同按位或的数量。

```cpp
class Solution {
public:
    /*
        LogTrick 模板题(模板要背熟):

        cur 存所有以当前 i 结尾的 OR 值。
        all 存全局出现过的 OR 值。
        每来一个 x，把 cur 里的值全部 | x，再加 x 本身

        复杂度: O(n log U)
    */
    int subarrayBitwiseORs(vector<int>& arr) {
        vector<pair<int, long long>> cur;  // {value, count}
        unordered_set<int> all;

        auto add = [&] (vector<pair<int, long long>> &v, int value, int count) -> void {
            if (!v.empty() && v.back().first == value)
                v.back().second += count;
            else
                v.push_back({value, count});
        };
        
        for (int x: arr) {
            vector<pair<int, long long>> nxt;
            
            add(nxt, x, 1);
            for (auto [value, count]: cur)
                add(nxt, value | x, count);
            
            cur = move(nxt);

            for (auto [value, count]: cur)
                all.insert(value);
        }

        return (int)all.size();
    }
};
```
