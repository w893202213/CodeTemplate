### tarjan 算法在有向图中找所有的 SCC (强连通分量)的缩点算法模板
#### 本题是洛谷 P2341 受欢迎的牛 G , 其实就是求缩点后唯一的出度为 0 的 scc 的大小
#### 这一类题的变体很多：
``` markdown
问能被所有点到达：找唯一出度为 0 的 SCC。
问能到达所有点：找唯一入度为 0 的 SCC。
问任意两点间或者 a 能到 b, 或者 b 能到 a: 判断缩点后的 DAG 是否为一条单链。

用模板都能快速解决 ~~~ 
```

代码：
```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <queue>

using namespace std;

typedef long long LL;

// tarjan scc 模板 (1-based) 
struct SCC {
    int n;
    vector<vector<int>> g;
    vector<int> dfn, low, stk, inStack;
    vector<int> belong, sz;
    int timer = 0, sccCnt = 0;
    
    SCC(int n): n(n), g(n + 1), dfn(n + 1), low(n + 1), inStack(n + 1), belong(n + 1), sz(n + 1) {}
    
    void addEdge(int u, int v) {
        g[u].push_back(v);
    }
    
    void dfs(int u) {
        dfn[u] = low[u] = ++ timer;
        stk.push_back(u);
        inStack[u] = 1;
        
        for (int v: g[u]) {
            if (dfn[v] == 0) {
                dfs(v);
                low[u] = min(low[u], low[v]);
            } else if (inStack[v] == 1) {
                low[u] = min(low[u], dfn[v]);
            }
        }
        
        if (dfn[u] == low[u]) {
            sccCnt ++;
            while (true) {
                int x = stk.back();
                stk.pop_back();
                inStack[x] = 0;
                belong[x] = sccCnt;
                sz[sccCnt] ++;
                if (x == u)  break;
            }
        }
    }
    
    void run() {
        for (int i = 1; i <= n; i ++ )
            if (dfn[i] == 0)
                dfs(i);
    }
};

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);

    int n, m;
    cin >> n >> m;
    
    SCC scc(n);
    int u, v;
    while (m -- ) {
        cin >> u >> v;
        scc.addEdge(u, v);
    }
    
    scc.run();  // 不要忘记调用 run() 函数
    
    // 找缩点后唯一的出度为 0 的 SCC 的大小
    vector<int> dout(scc.sccCnt + 1, 0);
    for (int u = 1; u <= n; u ++ )
        for (int v: scc.g[u]) {
            int cu = scc.belong[u];
            int cv = scc.belong[v];
            if (cu != cv) {
                dout[cu] ++;
            }
        }
    
    int cnt = 0, res = 0;
    for (int i = 1; i <= scc.sccCnt; i ++ )
        if (dout[i] == 0) {
            cnt ++;
            res = scc.sz[i];
        }
    
    if (cnt != 1) {
        cout << 0 << endl;
    } else {
        cout << res << endl;
    }
    
    return 0;
}
```
