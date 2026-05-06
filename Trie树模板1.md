#### 最基础的 Trie 树模板题：力扣 208 

代码：

```cpp
const int N = 60010;

int son[N][26];
int cnt[N];
int idx;

class Trie {
public:
    Trie() {
        memset(son, 0, sizeof son);
        memset(cnt, 0, sizeof cnt);
        idx = 0;
    }
    
    void insert(string word) {
        int p = 0;
        for (auto &ch: word) {
            if (son[p][ch - 'a'] == 0)
                son[p][ch - 'a'] = ++ idx;
            p = son[p][ch - 'a'];
        }
        cnt[p] ++;
    }
    
    bool search(string word) {
        int p = 0;
        for (auto &ch: word) {
            if (son[p][ch - 'a'] == 0)
                return false;
            p = son[p][ch - 'a'];
        }
        return cnt[p];
    }
    
    bool startsWith(string prefix) {
        int p = 0;
        for (auto &ch: prefix) {
            if (son[p][ch - 'a'] == 0)
                return false;
            p = son[p][ch - 'a'];
        }
        return true;
    }
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */
```
