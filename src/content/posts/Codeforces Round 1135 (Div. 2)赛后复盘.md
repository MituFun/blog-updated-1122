---
title: Codeforces Round 1135 (Div. 2)赛后复盘
published: 2026-11-15
description: '如题所示'
image: ''
tags: ['算法', '数学', '杂谈']
category: '竞赛'
draft: false 
lang: ''
---

赛时只写出来了 ABCD。

怎么每道题都是结论啊。

### A. Composite Permutation

非常简单的一道构造题。

考虑到 $i\times p_i$ 一定要为合数，容易发现只有 $i=1,p_i=1$ 时好像不太合适。

此外，更可以发现 $n\leq 3$ 的情况下不存在解。

$n=4$ 时仅存在：$p=\{4,2,3,1\}$ 唯一解。

$n>4$ 时完全可以构造：$p=\{4,2,3,1,5,6,7,8,9\cdots\}$，对于 $i>4$ 时，$i\times p_i=i^2$，一定为合数。

就如此构造，不贴代码了。

### B. Diverse String

这题挺巧妙的。

注意一个性质：平推一个区间内的元素，对于所谓的 Diverse String 而言，只不过是修改了左端点和右端点的元素。

举例：故考虑 $[l,r]$ 平推 $1$，相当于将 $l,r$ 两点处的值修改为 $1$。那为什么不考虑 $(l,r)$ 区间内的推平操作呢？因为这并不会影响同为推平 $1$ 操作的 Diverse String 个数，因为他们区间内都是推平为 $1$，**不同点是在端点。**

推平 $0$ 同理。

那么有多少个 Diverse String 呢？令字符串中 $1$ 的个数为 $\text{cnt1}$，$0$ 的个数为 $\text{cnt0}$，不难想到公式：

$$\text{ans}=1+\dfrac{\text{cnt1(cnt1+1)}}{2}+\dfrac{\text{cnt0(cnt0+1)}}{2}$$

为什么是 $\text{cnt}+1$ 的组合数呢？因为可以只修改一个数，相当于另外一个端点是自己。

但是题目中允许修改 $k$ 个值。我们期望上式答案最大化，根据性质有：$\text{cnt1}=n-\text{cnt0}$ 将上式化简得到（为了简单点，$x=\text{cnt1}$）：

$$\text{ans}=\dfrac{2x^2-2nx+(n^2+n+2)}{2}$$

容易发现，在 $n$ 为常数的情况下，此为对称轴为 $\dfrac{n}{2}$ 的二次函数，故当 $x$ 越偏激的时候，$\text{ans}$ 越有最大值。

故我们的目的就是要让 $\text{cnt1}$ 或是 $\text{cnt0}$ 在修改后存在最大值。

很简单，我们将二者中最小的那位经可能的转化为较大的那一位，答案即可有最大值。

（实际上代码中 $\text{cnt0,cnt1}$ 并不需要都求了，求出一个可以推另一个。但是当时考试的时候没太细想，赶时间就一块求了。）

```cpp
#include <iostream>
#include <cstring>
#include <string>
#include <algorithm>
#include <cmath>
#include <climits>
#include <vector>
using namespace std;

#define int long long
// #define cout cout << ">>>"

signed main() {
	cin.tie(0)->sync_with_stdio(0);
	int t;
	cin >> t;
	while (t--) {
		int n, k;
		cin >> n >> k;
		string s;
		cin >> s;
		int cnt1, cnt0;
		cnt1 = cnt0 = 0;
		for (auto c : s) {
			if (c == '0')
				cnt0++;
			if (c == '1')
				cnt1++;
		}
		if (cnt0 < cnt1)
			swap(cnt0, cnt1);
		cnt0 += min(k, cnt1);
		cnt1 -= min(k, cnt1);
		cout << 1 + (cnt0 * (cnt0 + 1)) / 2 + (cnt1 * (cnt1 + 1)) / 2 << endl;
	}
	return 0;
}
```

### C. Max Substitution

这题依旧是个结论题。

从大到小排序，对于排序后的第 $i$ 个元素 $a_i$：如果我们能修改到它，那么在修改它之前，前面的 $i-1$ 个元素必须全部小于 $a_i$。修改它之后，它自己也会小于 $a_i$。因此前 $i$ 个位置，每个位置都有 $a_i-1$ 种选择。

很容易想到公式：

$$
\text{ans}=1+\sum_{i=1}^{n}(a_i-1)^i
$$

就是注意一下当当前 $a_i=a_{i+1}$ 的时候就要停止累计了，故有的时候 $i$ 算不到 $n$。

C 没 B 难啊，C 挺符合直觉的，B 看不出那个性质就废了。

```cpp
#include <iostream>
#include <cstring>
#include <string>
#include <algorithm>
#include <cmath>
#include <climits>
#include <vector>
using namespace std;

#define int long long
// #define cout cout << ">>>"

const int mod = 1e9 + 7;

int ksm(int a, int b) {
	int ans = 1;
	while (b) {
		if (b & 1) {
			ans *= a;
			ans %= mod;
		}
		a *= a;
		a %= mod;
		b >>= 1;
	}
	return ans;
}

signed main() {
	cin.tie(0)->sync_with_stdio(0);
	int t;
	cin >> t;
	while (t--) {
		int n;
		cin >> n;
		vector<int> a;
		for (int i = 0; i < n; i++) {
			int t;
			cin >> t;
			a.push_back(t);
		}
		sort(a.begin(), a.end(), greater<int>());

		int ans = 1;
		for (int i = 0; i < n; i++) {
			if (i + 1 < n && a[i] == a[i + 1]) {
				break;
			}
			ans = (ans + ksm(a[i] - 1, i + 1)) % mod;
		}
		cout << ans << endl;
	}
	return 0;
}
```

### D. Reverse Partition

这题干出来给我猛猛上分了。

考虑到分割点必存在性质（$\text{pre}$ 前缀和，$\text{suf}$ 后缀和）：

$$\text{pre}_i-\text{suf}_{n-i+1}=0$$

故分割点也一定镜像对称。

不妨令 $d_i=\text{pre}_i-\text{suf}_{n-i+1}$。

对于将 $a_i$ 修改为 $v$，相当于将区间 $[i,n-i+1]$ 区间内的 $d$ 都增加 $v$。

故为了统计修改后的 $d_i=0$ 的个数，我们相当于是查找区间 $[i,n-i+1]$ 内 $d_i=-v$ 的个数再加上其他地方 $d_i=0$ 的个数。

区间修改，区间查询，线段树。

注意到我们是要找 $d_i$ 等于某个具体值的个数，我们线段树内维护的值可以是 `map<int, int>`，存的是某个数出现的个数。（当然，我们可以离散化做权值线段树，但是这题时间给的很充裕，没必要）

我们建树的时候直接统计好 0 的个数，然后**可以分割的点的个数就是初始 0 的个数 $+d_i=-v$ 的个数 $-$ $[i,n-i+1]$ 内 0 的个数。**

然后知道了可以分割点的个数直接算组合数。但是数巨大要取模，考虑逆元。

```cpp
#include <iostream>
#include <cstring>
#include <string>
#include <algorithm>
#include <cmath>
#include <climits>
#include <vector>
#include <map>
using namespace std;

#define int long long
// #define cout cout << ">>>"

const int maxn = 2e5 + 10;
int a[maxn];
int pre[maxn], suf[maxn];
int d[maxn];

map<int, int> tree[maxn * 2];

int ls(int id) { return id << 1; }
int rs(int id) { return id << 1 | 1; }

void pushup(int id) {
	tree[id] = tree[ls(id)];
	for (auto k : tree[rs(id)]) {
		tree[id][k.first] += k.second;
	}
}
int cntV(int id, int v) {
	auto it = tree[id].find(v);
	if (it == tree[id].end()) {
		return 0;
	} else {
		return it->second;
	}
}
void build(int id, int l, int r, int n) {
	if (l == r) {
		tree[id][d[l]] = (2 * l == n ? 1 : 2);
		return;
	}
	int mid = (l + r) >> 1;
	build(ls(id), l, mid, n);
	build(rs(id), mid + 1, r, n);
	pushup(id);
}
int query(int id, int l, int r, int x, int y, int v) {
	if (x <= l && r <= y) {
		return cntV(id, -v) - cntV(id, 0);
	}
	int mid = (l + r) >> 1;
	int ans = 0;
	if (x <= mid) {
		ans += query(ls(id), l, mid, x, y, v);
	}
	if (y > mid) {
		ans += query(rs(id), mid + 1, r, x, y, v);
	}
	return ans;
}

const int mod = 1e9 + 7;
int ksm(int a, int b) {
	int ans = 1;
	while (b) {
		if (b & 1) {
			ans *= a;
			ans %= mod;
		}
		a *= a;
		a %= mod;
		b >>= 1;
	}
	return ans;
}

int jc[maxn], invjc[maxn];
void init() {
	jc[0] = 1;
	for (int i = 1; i < maxn; i++) {
		jc[i] = (jc[i - 1] * i) % mod;
	}
	invjc[maxn - 1] = ksm(jc[maxn - 1], mod - 2);
	for (int i = maxn - 2; i >= 0; i--) {
		invjc[i] = invjc[i + 1] * (i + 1) % mod;
	}
}
int C(int n, int k) {
	if (n < k)
		return 0;
	return ((jc[n] * invjc[k]) % mod * invjc[n - k]) % mod;
}

signed main() {
	cin.tie(0)->sync_with_stdio(0);
	int t;
	cin >> t;
	init();
	while (t--) {
		int n, m, q;
		cin >> n >> m >> q;
		for (int i = 1; i <= n; i++) {
			cin >> a[i];
			pre[i] = pre[i - 1] + a[i];
		}
		suf[n + 1] = 0;
		for (int i = n; i >= 1; i--) {
			suf[i] = suf[i + 1] + a[i];
		}
		for (int i = 1; i <= n / 2; i++) {
			d[i] = pre[i] - suf[n - i + 1];
		}

		int origincnt0 = 0;
		if (n / 2 > 0) {
			build(1, 1, n / 2, n);
			origincnt0 = cntV(1, 0);
		}
		while (q--) {
			int i, v;
			cin >> i >> v;
			int mirrori = n - i + 1;
			int cnt0 = origincnt0;
			if (i != mirrori && v != 0) {
				int l = min(i, mirrori);
				int add = (i < mirrori ? v : -v);
				cnt0 += query(1, 1, n / 2, l, n / 2, add);
			}
			cout << C(cnt0, m - 1) << endl;
		}
		for (int i = 1; i <= n * 2 + 5; i++) {
			tree[i].clear();
		}
	}
	return 0;
}
```