# 数据结构-day1

### 并查集

优化方式

1. 路径压缩
2. 按秩合并 (merge时size小的当儿子)

### 可撤销并查集

路径压缩是无法撤销的。 但按秩合并可以。 所以只写按秩合并就行。记一下 merge 的时候连的哪条边。

时间复杂度 $O(nlogn)$

```cpp
template <const int N> struct dsu
{
    int f[N], g[N];
    stack<pair<int *, int>> s;
    void init(int n)
    {
        for (int i = 0; i <= n; i++)
            f[i] = i, g[i] = 1;
    }
    int get(int u)
    {
        return f[u] == u ? u : get(f[u]);
    }
    bool find(int u, int v)
    {
        return get(u) == get(v);
    }
    void merge(int u, int v)
    {
        u = get(u), v = get(v);
        if (u == v)
            return;
        if (g[u] > g[v])
            swap(u, v);
        s.push({f + u, f[u]}), f[u] = v;
        s.push({g + v, g[v]}), g[v] += g[u];
    }
    unsigned tag()
    {
        return s.size();
    }
    void undo(unsigned tag)
    {
        while (s.size() > tag)
            *s.top().first = s.top().second, s.pop();
    }
};
```

### Kruskal 重构树

并查集在合并集合的时候是直接加一条边上去。 考虑另一种做法：找到 $u,v$ 所在树的根节点 $u',v'$，然后新建一个结点 $z$ 作为 $u',v'$ 的父亲。 这样我们就得到了 kruskal 重构树。 原树上的点在重构树上都是叶子结点，并且 kruskal 重构树是一棵二叉树。可以理解为一种特殊的三度化(树转二叉树)
