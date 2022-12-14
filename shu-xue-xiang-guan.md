# 😵 数学相关



### 概率论

#### 期望的定义

$$
E(X) = \sum_{p\ in\ X} P(p)D(p)
$$

其中 $$P(p)$$ 是 $$p$$ 的概率， $$D(p)$$ 是 $$p$$ 事件的权值 例如:

> clb 给进了省队的同学发钱，有 20% 的概率发 10000 元, 80% 的概率发 100 元，问期望获得多少元?

1. 事件 $$X_{1}$$ : 得到 10000 元，概率为 20%
2. 事件 $$X_{2}$$ : 得到 100 元， 概率为 80%

期望：

$$
E(得到的钱) = 10000 \times 20\% + 100 \times 80\% = 2080 元
$$

#### 期望的性质

> X, Y 是随机变量

$$
不要求独立： E(X + Y) = E(X) + E(Y)
$$

$$
要求 X, Y 独立: E(XY) = E(X) \times E(Y)
$$

> $$PS: 令 P(X | Y) 表示在 Y 发生时 X 的概率$$

#### 一个定理

$$
P(X | Y) = \cfrac{P(Y | X) \times P(X)}{P(Y)}
$$

#### 独立的定义

什么是独立呢？就是**互不影响**呗。 但是不太严谨。

严谨一点的说法:

$$
X, Y 独立 \Leftrightarrow P(XY) = P(X)P(Y) \\ X, Y 独立 \Leftrightarrow P(X | Y) = P(X) \\ X, Y 独立 \Leftrightarrow P(Y | X) = P(Y) \\
$$

### 线性代数

#### 线性空间的定义

设V是一个非空集合，P是一个域。若： 1.在V中定义了一种运算，称为加法，即对V中任意两个元素a与b都按某一法则对应于V内惟一确定的一个元素a+b，称为a与b的和. 2.在P与V的元素间定义了一种运算，称为纯量乘法(亦称数量乘法)，即对V中任意元素a和P中任意元素k，都按某一法则对应V内惟一确定的一个元素ka，称为k与a的积。 3.加法与纯量乘法满足以下条件：

1. a+b=b+a，对任意a，b∈V.
2. a+(b+γ)=(a+b)+γ，对任意a，b，γ∈V.
3. 存在一个元素0∈V，对一切a∈V有a+0=a，元素0称为V的零元.
4. 对任一a∈V，都存在b∈V使a+b=0，b称为a的负元素，记为-a.
5. 对P中单位元1，有1a=a(a∈V).
6. 对任意k，l∈P，a∈V有(kl)a=k(la).
7. 对任意k，l∈P，a∈V有(k+l)a=ka+la.
8. 对任意k∈P，a，b∈V有k(a+b)=ka+kb， 则称V为域P上的一个线性空间，或向量空间。

简单的表示：

线性空间定义为一个集合 S 和一个域 F ,S 上有对元素定义的加法,S 和 F 之间定义 了乘法(数乘),满足:

1. $$S$$ 上加法满足交换和结合律
2. $$S$$ 存在单位元 $$0_{S}$$ ,每个元素 $$a$$ 存在逆元 $$−a$$
3. $$1_{F} ⋅ a = a, p ⋅ 0_{S} = 0_{S}$$
4. 对于 $$p,q\in F, a, b \in S$$ 满足 $$(p + q)a = pa + qa, p(a + b) = pa + pb, (pq)a = p(qa)$$.

则称 S 为 F 上的线性空间。

> 数域：设F是一个含有0和1的数集。如果F对于数的四则运算都封闭，那么称系统（F；+，－，×，÷）为一个数域。 例如有理数Q是一个域，但是非负整数Z+就不是域（它不满足除法和减法）

#### 线性相关和线性无关

设 S 为 F 上的线性空间，$$\beta = \{b_{1},\cdots,b_{m}\}$$ 为 S 的一个子集，若存在不全为 0 的 $$p_{1}, \cdots p_{m} \in F$$ 使得 $$p_{1}b_{1} + \cdots + p_{m}b_{m} = 0$$,则称 $$\beta$$ 线性相关。否则就是线性无关。

如果 $$S$$ 有一组线性无关的子集$$\alpha$$可以线性表示出所有 $$S$$ 汇总的元素，则 $$\alpha$$ 为 $$S$$ 的一组基。

#### 线性基

> 实际一点的东西 $$\cdots$$

线性基是一个数的集合，并且每个序列都拥有至少一个线性基，取线性基中若干个数异或起来可以得到原序列中的任何一个数。

线性基三大性质

1. 原序列里面的任意一个数都可以由线性基里面的一些数异或得到
2. 线性基里面的任意一些数异或起来都不能得到 0 0 0
3. 线性基里面的数的个数唯一，并且在保持性质一的前提下，数的个数是最少的

#### 怎么求？

设数组 d 表示序列 a 的线性基，下标从 0 开始算

```cpp
using ll = long long;
void insert(ll x) // 插入
{
	for (int i = 60; i >= 0; i--)
		if((x >> i) & 1)
		{
			if(d[i]) x ^= d[i];
			else
			{
				d[i] = x;
				break;
			}
		}
}
```

#### 最大/最小

```cpp
ll maxium()
{
    ll res = 0;
    for (int i = 60; i >= 0; i--)
		if ((res ^ d[i]) > res) res ^= d[i];
    return res;
} 
ll minium()
{
	return *min_element(d, d + 61)'
}
```
