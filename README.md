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

### 拓扑排序
```cpp
vector<int> G[maxn];
int deg[maxn];
void addEdge(int u, int v) {
    G[u].push_back(v);
    deg[v]++;
}
int seq[maxn];
bool toposort(int n) {
    queue<int> q;
    for (int i = 1; i <= n; i++) {
        if (deg[i] == 0)
        {
            q.push(i);
        }
    }
    int ncnt = 0;
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        seq[++ncnt] = u;
        for (int i = 0; i < G[u].size(); i++) {
            int v = G[u][i];
            deg[v] -= 1;
            if(deg[v] == 0) {
                q.push(v);
            }
        }
    }
    return ncnt == n;
}
```

### Kruskal
```cpp
// -->> 并查集 <<-- //
struct Edge {
    int u, v, w;
} e[maxm];
int ecnt;
int Kruskal() {
    init()
    sort(e + 1, e + ecnt + 1, cmp);
    int cnt = 0;
    int ans = 0;
    for (int i = 1; i <= ecnt; i++) {
        int u = e[i].u;
        int v = e[i].v;
        u = get(u);
        v = get(v);
        if(u != v) {
            fa[u] = v;
            cnt++;
            ans = max(ans, e[i].w);
        }
    }
    return (cnt == n ? ans : -1);
}
```

### 快速幂
```cpp
int qpow(int a, int n){
    int ans = 1;
    while(n){
        if(n&1)        //如果n的当前末位为1
            ans *= a;  //ans乘上当前的a
        a *= a;        //a自乘
        n >>= 1;       //n往右移一位
    }
    return ans;
}
```

### 线段数组[（视频讲解）](https://b23.tv/7ohQVP0)
```cpp
int b[N] = {}, N;
inline int lowbit(int x) {
	return x & (-x);
}
void add(int p, int x) {
	while (p < N) {
		b[p] += x;
		p += lowbit(p);
	}
}
int count(int p) {
	int res = 0;
	while (p) {
		res += p[b];
		p -= lowbit(p);
	}
	return res;
}
```


### 运算怒优先级

<table style="font-size:15px" border="1" cellspacing="5" cellpadding="5"><tbody><tr><th class="code-table-th">优先级</th><th class="code-table-th">操作符</th><th class="code-table-th">描述</th><th class="code-table-th">例子</th><th class="code-table-th">结合性</th></tr><tr><td class="code-table-td">1</td><td class="code-table-td">()<br> []<br> -&gt;<br> .<br> ::<br> ++<br> --</td><td class="code-table-td">调节优先级的括号操作符<br> 数组下标访问操作符<br> 通过指向对象的指针访问成员的操作符<br> 通过对象本身访问成员的操作符<br> 作用域操作符<br> 后置自增操作符<br> 后置自减操作符</td><td class="code-table-td">(a + b) / 4;<br> array[4] = 2;<br> ptr-&gt;age = 34;<br> obj.age = 34;<br> Class::age = 2;<br> for( i = 0; i &lt; 10; i++ ) ...<br> for( i = 10; i &gt; 0; i-- ) ...</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">2</td><td class="code-table-td">!<br> ~<br> ++<br> --<br> -<br> +<br> *<br> &amp;<br> (type)<br> <a href="http://www.cppreference.com/keywords/sizeof.html">sizeof</a></td><td class="code-table-td">逻辑取反操作符<br> 按位取反(按位取补) <br> 前置自增操作符<br> 前置自减操作符<br> 一元取负操作符<br> 一元取正操作符<br> 解引用操作符<br> 取地址操作符<br> 类型转换操作符<br> 返回对象占用的字节数操作符</td><td class="code-table-td">if( !done ) ...<br> flags = ~flags;<br> for( i = 0; i &lt; 10; ++i ) ...<br> for( i = 10; i &gt; 0; --i ) ...<br> int i = -1;<br> int i = +1;<br> data = *ptr;<br> address = &amp;obj;<br> int i = (int) floatNum;<br> int size = sizeof(floatNum);</td><td class="code-table-td">从右到左</td></tr><tr><td class="code-table-td">3</td><td class="code-table-td">-&gt;*<br> .*</td><td class="code-table-td">在指针上通过指向成员的指针访问成员的操作符<br> 在对象上通过指向成员的指针访问成员的操作符</td><td class="code-table-td">ptr-&gt;*var = 24;<br> obj.*var = 24;</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">4</td><td class="code-table-td">*<br> /<br> %</td><td class="code-table-td">乘法操作符<br> 除法操作符<br> 取余数操作符</td><td class="code-table-td">int i = 2 * 4;<br> float f = 10 / 3;<br> int rem = 4 % 3;</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">5</td><td class="code-table-td">+<br> -</td><td class="code-table-td">加法操作符<br> 减法操作符</td><td class="code-table-td">int i = 2 + 3;<br> int i = 5 - 1;</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">6</td><td class="code-table-td">&lt;&lt;<br> &gt;&gt;</td><td class="code-table-td">按位左移操作符<br> 按位右移操作符</td><td class="code-table-td">int flags = 33 &lt;&lt; 1;<br> int flags = 33 &gt;&gt; 1;</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">7</td><td class="code-table-td">&lt;<br> &lt;=<br> &gt;<br> &gt;=</td><td class="code-table-td">小于比较操作符<br> 小于或等于比较操作符<br> 大于比较操作符<br> 大于或等于比较操作符</td><td class="code-table-td">if( i &lt; 42 ) ...<br> if( i &lt;= 42 ) ...<br> if( i &gt; 42 ) ...<br> if( i &gt;= 42 ) ...</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">8</td><td class="code-table-td">==<br> !=</td><td class="code-table-td">等于比较操作符<br> 不等于比较操作符</td><td class="code-table-td">if( i == 42 ) ...<br> if( i != 42 ) ...</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">9</td><td class="code-table-td">&amp;</td><td class="code-table-td">按位与操作符</td><td class="code-table-td">flags = flags &amp; 42;</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">10</td><td class="code-table-td">^</td><td class="code-table-td">按位异或操作符</td><td class="code-table-td">flags = flags ^ 42;</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">11</td><td class="code-table-td">|</td><td class="code-table-td">按位或操作符</td><td class="code-table-td">flags = flags | 42;</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">12</td><td class="code-table-td">&amp;&amp;</td><td class="code-table-td">逻辑与操作符</td><td class="code-table-td">if( conditionA &amp;&amp; conditionB ) ...</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">13</td><td class="code-table-td">||</td><td class="code-table-td">逻辑或操作符</td><td class="code-table-td">if( conditionA || conditionB ) ...</td><td class="code-table-td">从左到右</td></tr><tr><td class="code-table-td">14</td><td class="code-table-td">? :</td><td class="code-table-td">三元条件操作符</td><td class="code-table-td">int i = (a &gt; b) ? a : b;</td><td class="code-table-td">从右到左</td></tr><tr><td class="code-table-td">15</td><td class="code-table-td">=<br> +=<br> -=<br> *=<br> /=<br> %=<br> &amp;=<br> ^=<br> |=<br> &lt;&lt;=<br> &gt;&gt;=</td><td class="code-table-td">赋值操作符<br> 复合赋值操作符(加法)<br> 复合赋值操作符(减法)<br> 复合赋值操作符(乘法)<br> 复合赋值操作符(除法)<br> 复合赋值操作符(取余)<br> 复合赋值操作符(按位与)<br> 复合赋值操作符(按位异或)<br> 复合赋值操作符(按位或)<br> 复合赋值操作符(按位左移)<br> 复合赋值操作符(按位右移)</td><td class="code-table-td">int a = b;<br> a += 3;<br> b -= 4;<br> a *= 5;<br> a /= 2;<br> a %= 3;<br> flags &amp;= new_flags;<br> flags ^= new_flags;<br> flags |= new_flags;<br> flags &lt;&lt;= 2;<br> flags &gt;&gt;= 2;</td><td class="code-table-td">从右到左</td></tr><tr><td class="code-table-td">16</td><td class="code-table-td">,</td><td class="code-table-td">逗号操作符</td><td class="code-table-td">for( i = 0, j = 0; i &lt; 10; i++, j++ ) ...</td><td class="code-table-td">从左到右</td></tr></tbody></table>
