#### 01 Trie 不是维护字符串，而是维护整数的二进制位
#### 模板题是 Acwing 143 最大异或对

代码：

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <string>

using namespace std;

const int N = 1e5 + 10;


int son[N * 31][2];
int idx = 0;

void insert(int x) {
    int p = 0;
    for (int i = 30; i >= 0; i -- ) {
        int u = (x >> i) & 1;
        if (son[p][u] == 0)
            son[p][u] = ++ idx;
        p = son[p][u];
    }
}

// 返回与 x 异或值最大的结果
int queryMaxXor(int x) {
    int p = 0;
    
    int res = 0;
    for (int i = 30; i >= 0; i -- ) {
        int u = (x >> i) & 1;
        int want = u ^ 1;
        
        if (son[p][want]) {
            res += (1 << i);
            p = son[p][want];
        } else {
            p = son[p][u];
        }
    }
    
    return res;
}


int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);
    
    int n;
    cin >> n;
    
    int x;
    
    int res = 0;
    while (n -- ) {
        cin >> x;
        insert(x);
        res = max(res, queryMaxXor(x));
    }
    
    cout << res << endl;
    
    return 0;
}
```
