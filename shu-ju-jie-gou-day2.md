# 数据结构-day2



### Fibonacci

$$
\sum_{i = 1}^{n} F[i] = F[n + 2] - 1;
$$

### K-D Tree

K-D Tree 是对 K 维空间里 n 个建立树形结构。一般用于乱搞。 考虑 2-D Tree 的建树过程。 build(l, r, x) 表示把 以 x 坐标为优先关键字来建 2-D Tree。 build(l, r, y) 同理，以 y 坐标为 优先关键字。

设\[l, r]区间中点为mid，那么build (l,r,x) 会 1.找到pl，... , pr中坐标第mid -l＋1大的点（即中间的那个点)，作为这个区间2-D Tree的根节点 2.该节点的左子树x坐标都小于等于它，右子树的x坐标都大于等它。 同时我们调整p\[l, r]的排序使得pmid成为一个快排划分。 3.该节点的左子树为build (l, mid - 1, y)，右子树为 build (mid + 1, r, y)。 build(l, r, y)同理，只不过递归的时候改成x

```cpp
struct Point
{
    int x, y, id;
    static bool byX(Point p1, Point p2)
    {
        return p1.x < p2.x;
    }
    static bool byY(Point p1, Point p2)
    {
        return p1.y < p2.y;
    }
};
void update(int u, int v)
{
    Lx[u] = min(Lx[u], Lx[v]), Rx[u] = max(Rx[u], Rx[v]);
    Ly[u] = min(Ly[u], Ly[v]), Ry[u] = max(Ry[u], Ry[v]);
}
int build(int l, int r, bool tag)
{
    if (l > r)
        return 0;
    int mid = (l + r) / 2;
    if (l < r)
    {
        nth_element(a + l, a + r, a + mid, tag ? Point::byX : Point::byY);
        lc[mid] = build(l, mid - 1, !tag), rc[mid] = build(mid + 1, r, !tag);
    }
    Lx[mid] = Rx[mid] = a[mid].x, Ly[mid] = Ry[mid] = a[mid].y;
    if (lc[mid])
        update(mid, lc[mid]);
    if (rc[mid])
        update(mid, rc[mid]);
    return mid;
}
```
