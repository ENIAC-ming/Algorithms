# Algorithms

一些算法

### [A10](A10)

### [A11](A11)

### 大整数
```cpp
bool mygreater(const string& a, const string& b)
{
	if (a.size() == b.size())
	{
		return a >= b;
	}
	return a.size() >= b.size();
}

string add(string a, string b)
{
	size_t len = max(a.size(), b.size()) + 1;
	a.insert(0, len - a.size(), '0');
	b.insert(0, len - b.size(), '0');

	string ans;
	ans.reserve(len);
	int extra = 0;
	for (size_t i = len - 1; i != size_t(-1); --i)
	{
		int tmp = (a[i] - '0') + (b[i] - '0') + extra;
		ans.push_back(tmp % 10 + '0');
		extra = tmp / 10;
	}
	ans.erase(ans.find_last_not_of('0') + 1);
	return ans.empty() ? "0" : string(ans.rbegin(), ans.rend());
}

string myminus(string a, string b)
{
	size_t len = max(a.size(), b.size());
	a.insert(0, len - a.size(), '0');
	b.insert(0, len - b.size(), '0');

	bool isneg = false;
	if (a < b)
	{
		swap(a, b);
		isneg = true;
	}

	string ans;
	ans.reserve(len);
	int extra = 0;
	for (size_t i = len - 1; i != size_t(-1); --i)
	{
		int tmp = 10 + a[i] - b[i] - extra;
		ans.push_back(tmp % 10 + '0');
		extra = tmp < 10 ? 1 : 0;
	}
	ans.erase(ans.find_last_not_of('0') + 1);
	return ans.empty() ? "0" : (isneg ? "-" : "") + string(ans.rbegin(), ans.rend());
}

string times(string a, string b)
{
	reverse(a.begin(), a.end());
	reverse(b.begin(), b.end());
	a += '0';
	b += '0';
	string ans(a.size() + b.size(), '0');
	for (size_t i = 0; i < a.size(); ++i)
	{
		int extra = 0;
		for (size_t j = 0; j < b.size(); ++j)
		{
			int tmp = (ans[i + j] - '0') + (a[i] - '0') * (b[j] - '0') + extra;
			ans[i + j] = tmp % 10 + '0';
			extra = tmp / 10;
		}
	}
	ans.erase(ans.find_last_not_of('0') + 1);
	return ans.empty() ? "0" : string(ans.rbegin(), ans.rend());
}

pair<string, string> divide2(string a, string b)//高精除以高精
{
	size_t lena = a.size();
	string ans;
	ans.reserve(lena);
	for (size_t zeros = lena - b.size(); zeros < lena; --zeros)
	{
		string divisor = b + string(zeros, '0');
		ans.push_back('0');
		while (mygreater(a, divisor))
		{
			a = myminus(a, divisor);
			++ans[ans.size() - 1];
		}
	}
	ans.erase(0, ans.find_first_not_of('0'));
	return make_pair(ans.empty() ? "0" : ans, a);
}

pair<string, long long> divide1(const string& a, long long b)//快除（高精除以整数）
{
	string ans;
	ans.reserve(a.size());
	long long dividend = 0;
	for (size_t i = 0; i < a.size(); ++i)
	{
		long long tmp = dividend * 10 + (a[i] - '0');
		ans.push_back(char(tmp / b + '0'));
		dividend = tmp % b;
	}
	ans.erase(0, ans.find_first_not_of('0'));
	return make_pair(ans.empty() ? "0" : ans, dividend);
}

long long gcd(long long a, long long b)
{
	return b == 0 ? a : gcd(b, a % b);
}
```

### 并查集
```cpp
void init() {
    for (int i = 1; i <= n; i++) {
        fa[i] = i;
    }
}
int get(int x) {
    if (fa[x] == x) {
        return fa[x];
    }
    fa[x] = get(fa[x]); //路径压缩
    return fa[x];
}
void merge(int x, int y) {
    x = get(x);
    y = get(y);
    if (x != y) { // 不在同一个集合
        fa[y] = x;
    }
}
```

### Dijkstra堆优化
```cpp
set<pair<int, int> > min_heap;
min_heap.insert(make_pair(0, s));
while (min_heap.size()) {
    int v = min_heap.begin() -> second; // 取出最近的点
    min_heap.erase(min_heap.begin()); // 删除首元素
    for (int i = h[v]; i != -1; i = edge[i].next) {
        int to = edge[i].to;
        if (d[to] > d[v] + edge[i].len) {
            min_heap.erase(make_pair(d[to], to)); // 先删除原来的元素
            d[to] = d[v] + edge[i].len; // 更新距离
            min_heap.insert(make_pair(d[to], to)); // 加入新的元素
        }
    }
}
```

### SPFA
```cpp
bool in_queue[MAX_N];
int d[MAX_N];  // 如果到顶点 i 的距离是 0x3f3f3f3f，则说明不存在源点到 i 的最短路
queue<int> q;
void spfa(int s) {
    memset(in_queue, 0, sizeof(in_queue));
    memset(d, 0x3f, sizeof(d));
    d[s] = 0;
    in_queue[s] = true; // 标记 s 入队
    q.push(s);
    while (!q.empty()) {
        int v = q.front();
        q.pop();
        in_queue[v] = false;
        for (int i = h[v]; i != -1; i = edge[i].next) {
            int to = edge[i].to;
            if (d[to] > d[v] + g[v][i].w) { // 更新
                d[to] = d[v] + g[v][i].w;
                if (!in_queue[to]) { // 如果之前没入队
                    q.push(to); // 入队
                    in_queue[to] = true; // 标记 to 入队
                }
            }
        }
    }
}
```

### Floyd
```cpp
int g[N][N];
void floyd(int n) {
    for (int k = 1; k <= n; k++) { // 中间点
        for (int i = 1; i <= n; i++) { // 起点
            for (int j = 1; j <= n; j++) { // 终点
                g[i][j] = min(g[i][j], g[i][k] + g[k][j]);
            }
        }
    }
}
```

### 链式前向星
```cpp
int head[n], tot = -1;					//定义入口和末尾 
struct Edge {int to, next, len;};		//定义边 
memset(head, -1, sizeof(head));			//初始化 
void addEdge(int x, int y, int len) {	//加边x --len--> y 
    edge[++tot] = (Edge){y, h[x], len}; 
    head[x] = tot; 
}
```
