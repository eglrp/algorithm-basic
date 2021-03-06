# 第九讲

## prim算法

```c++
int n;		// n表示点数
int g[N][N];		// 邻接矩阵，存储所有边
int dist[N];		// 存储其他点到当前最小生成树的距离
bool st[N];		// 存储每个点是否已经在生成树中


// 如果图不连通，则返回INF(值是0x3f3f3f3f), 否则返回最小生成树的树边权重之和
int prim() {
    memset(dist, 0x3f, sizeof dist);

    int res = 0;
    for (int i = 0; i < n; i ++ ) {
        int t = -1;
        for (int j = 1; j <= n; j ++ )
        	if (!st[j] && (t == -1 || dist[t] > dist[j]))
        		t = j;

		if (i && dist[t] == INF) return INF;

		if (i) res += dist[t];
		st[t] = true;

		for (int j = 1; j <= n; j ++ ) dist[j] = min(dist[j], g[t][j]);
	}

	return res;
}
```

## Kruskal算法

```c++
int n, m;		// n是点数，m是边数
int p[N];		// 并查集的父节点数组

// 存储边
struct Edge {
	int a, b, w;
	bool operator< (const Edge &W)const {
		return w < W.w;
	}
} edges[M];

// 并查集核心操作
int find(int x)	{
	if (p[x] != x) p[x] = find(p[x]);
	return p[x];
}

int kruskal() {
	sort(edges, edges + m);

	for (int i = 1; i <= n; i ++ ) p[i] = i;    // 初始化并查集

	int res = 0, cnt = 0;
	for (int i = 0; i < m; i ++ ) {
		int a = edges[i].a, b = edges[i].b, w = edges[i].w;

		a = find(a), b = find(b);
        // 如果两个连通块不连通，则将这两个连通块合并
		if (a != b)	{
            p[a] = b;
            res += w;
            cnt ++ ;
		}
	}

    if (cnt < n - 1) return INF;
    return res;
}
```

## 染色法判别二分图

```c++
int n;		// n表示点数
int h[N], e[M], ne[M], idx;		// 邻接表存储图
int color[N];		// 表示每个点的颜色，-1表示为染色，0表示白色，1表示黑色

// 参数：u表示当前节点，father表示当前节点的父节点（防止向树根遍历），c表示当前点的颜色
bool dfs(int u, int father, int c) {
    color[u] = c;
    for (int i = h[u]; i != -1; i = ne[i]) {
        int j = e[i];
        if (color[j] == -1) {
            if (!dfs(j, u, !c)) return false;
        }
        else if (color[j] == c) return false;
    }

    return true;
}

bool check() {
    memset(color, -1, sizeof color);
    bool flag = true;
    for (int i = 1; i <= n; i ++ )
        if (color[i] == -1)
            if (!dfs(i, -1, 0)) {
                flag = false;
                break;
            }
    return flag;
}

```

## 匈牙利算法

```c++
int n;		// n表示点数
int h[N], e[M], ne[M], idx;		// 邻接表存储所有边
int match[N];		// 存储每个点当前匹配的点
bool st[N];		// 表示每个点是否已经被遍历过

bool find(int x) {
    for (int i = h[x]; i != -1; i = ne[i]) {
        int j = e[i];
        if (!st[j]) {
            st[j] = true;
            if (match[j] == 0 || find(match[j])) {
                match[j] = x;
                return true;
            }
        }
    }
    return false;
}

// 求最大匹配数
int res = 0;
for (int i = 1; i <= n; i ++ ) {
    memset(st, false, sizeof st);
    if (find(i)) res ++ ;
}
```

