# 😁 板子

{% embed url="https://github.com/ozqs/code" %}

```cpp
#include <iostream>
#include <cstdio>

struct SetFastIO
{
    SetFastIO()
    {
        std::ios::sync_with_stdio(0);
        std::cin.tie(0), std::cout.tie(0);
    }
};

struct FastIO
{
    friend FastIO operator>>(FastIO io, char &x)
    {
        int c = getchar();
        while (isspace(c)) c = getchar();
        return io;
    }
    friend FastIO operator>>(FastIO io, std::string &x)
    {
        int c = getchar();
        while (isspace(c)) c = getchar();
        x = std::string();
        while (!isspace(c)) x = x + char(c), c = getchar();
        return io;
    }
    friend FastIO operator>>(FastIO io, int &x)
    {
        int c = getchar(), f = 1;
        x = 0;
        while (c < '0' || c > '9')
        {
            if (c == '-')
                f = -f;
            c = getchar();
        }
        while (c >= '0' && c <= '9')
        {
            x = (x << 3) + (x << 1) + c - 48;
            c = getchar();
        }
        x *= f;
        return io;
    }
    friend FastIO operator>>(FastIO io, long long &x)
    {
        int c = getchar(), f = 1;
        x = 0;
        while (c < '0' || c > '9')
        {
            if (c == '-')
                f = -f;
            c = getchar();
        }
        while (c >= '0' && c <= '9')
        {
            x = (x << 3) + (x << 1) + c - 48;
            c = getchar();
        }
        x *= f;
        return io;
    }
    friend FastIO operator<<(FastIO io, const long long x)
    {
        if (x > 9)
            io << (x / 10);
        putchar(x % 10 + 48);
        return io;
    }
    friend FastIO operator<<(FastIO io, const int x)
    {
        if (x > 9)
            io << (x / 10);
        putchar(x % 10 + 48);
        return io;
    }
    friend FastIO operator<<(FastIO io, const std::string x)
    {
        for (auto p : x)
            putchar(p);
        return io;
    }
    friend FastIO operator<<(FastIO io, const char x)
    {
        putchar(x);
        return io;
    }
    inline int get() { return getchar(); }
    inline void put(const char x) { putchar(x); }
};
```

```cpp

#include <algorithm>
#include <vector>
#include <string>
#include <iostream>
#include <iomanip>
#include <cassert>

using namespace std;

///继承vector解决位数限制(当前最大位数是9倍整型最大值)，操作方便（注意size()返回无符号长整型，尽量不要直接把size放入表达式）
struct BigInt : vector<long long>
{
    static const int WIDTH = 9;                  ///压位数，压9位以下 比较安全
    static const long long BASE = 1000000000;    ///单位基
    static const long long MAX_INT = ~(1 << 31); ///最大整型
    bool SIGN;
    ///初始化，同时也可以将低精度转高精度、字符串转高精度
    ///无需单独写高精度数和低精度数的运算函数，十分方便
    BigInt(long long n = 0) { *this = n; }
    BigInt(const string &str) { *this = str; }

    ///格式化，包括进位和去前导0，用的地方很多，先写一个
    BigInt &format(int fixlen = 1)
    { //去0后长度必须大于等于fixlen，给乘法用的
        while (size() > fixlen && !back())
            pop_back(); //去除最高位可能存在的0
        if (!back())
            SIGN = 0;
        for (int i = 1; i < size(); ++i)
        {
            (*this)[i] += (*this)[i - 1] / BASE;
            (*this)[i - 1] %= BASE;
        } //位内进位
        while (back() >= BASE)
        {
            push_back(back() / BASE);
            (*this)[size() - 2] %= BASE;
        }             //位外进位
        return *this; //为使用方便，将进位后的自身返回引用
    }

    ///归零
    void reset()
    {
        clear();
        SIGN = 0;
    }

    ///重载等于，初始化、赋值、输入都用得到
    BigInt operator=(long long n)
    {
        reset(); //必须先归零，而且这里因为继承了vector，所以不需要考虑vector的初始化导致clear是否能用的问题
        SIGN = n < 0;
        if (SIGN)
            n = -n;
        push_back(n);
        format();
        return *this;
    }
    BigInt operator=(const string &str)
    {
        reset();
        if (str.empty())
            push_back(0);
        SIGN = str[0] == '-';
        for (int i = str.length() - 1; i >= 0 + SIGN; i -= WIDTH)
        {
            long long tmp = 0;
            for (int j = max(i - WIDTH + 1, 0 + SIGN); j <= i; j++)
                tmp = (tmp << 3) + (tmp << 1) + (str[j] ^ 48);
            push_back(tmp);
        }
        format();
        return *this;
    }

    ///重载输入输出
    friend istream &operator>>(istream &is, BigInt &tmp)
    {
        string str;
        if (!(is >> str))
            return is;
        tmp = str;
        return is;
    }
    friend ostream &operator<<(ostream &os, const BigInt &tmp)
    {
        if (tmp.empty())
            os << 0;
        else
        {
            if (tmp.SIGN)
                os << '-';
            os << tmp[tmp.size() - 1];
        }
        for (int i = tmp.size() - 2; i >= 0; i--)
        {
            os << setfill('0') << setw(WIDTH) << tmp[i];
        }
        return os;
    }

    ///重载逻辑运算符，只需要小于，其他的直接代入即可
    ///常量引用当参数，避免拷贝更高效
    friend bool operator<(const BigInt &a, const BigInt &b)
    {
        if (a.SIGN ^ b.SIGN)
            return a.SIGN;
        if (a.size() != b.size())
            return a.SIGN ? a.size() > b.size() : a.size() < b.size();
        for (int i = a.size() - 1; i >= 0; i--)
            if (a[i] != b[i])
                return a.SIGN ? a[i] > b[i] : a[i] < b[i];
        return 0;
    }
    friend bool operator>(const BigInt &a, const BigInt &b) { return b < a; }
    friend bool operator>=(const BigInt &a, const BigInt &b) { return !(a < b); }
    friend bool operator<=(const BigInt &a, const BigInt &b) { return !(a > b); }
    friend bool operator!=(const BigInt &a, const BigInt &b) { return a < b || b < a; }
    friend bool operator==(const BigInt &a, const BigInt &b) { return !(a != b); }

    ///重载负号
    friend BigInt operator-(BigInt a) { return a.SIGN = !a.SIGN, a; }

    ///绝对值函数
    friend BigInt abs(BigInt a) { return a.SIGN ? (-a) : a; }

    ///加法，先实现+=，这样更简洁高效
    friend BigInt &operator+=(BigInt &a, const BigInt &b)
    {
        if (a.SIGN ^ b.SIGN)
            return a -= (-b);
        if (a.size() < b.size())
            a.resize(b.size());
        for (int i = 0; i < b.size(); i++)
            a[i] += b[i]; //被加数要最大位，并且相加时不要用未定义区间相加
        return a.format();
    }
    friend BigInt operator+(BigInt a, const BigInt &b) { return a += b; }
    friend BigInt &operator++(BigInt &a) { return a += 1; }
    friend BigInt operator++(BigInt &a, int)
    {
        BigInt old = a;
        ++a;
        return old;
    }

    ///减法，由于后面有交换，故参数不用引用
    friend BigInt &operator-=(BigInt &a, BigInt b)
    {
        if (a.SIGN ^ b.SIGN)
            return a += (-b);
        if (abs(a) < abs(b))
        {
            BigInt t = a;
            a = b;
            b = t;
            a.SIGN = !a.SIGN;
        }
        for (int i = 0; i < b.size(); a[i] -= b[i], i++)
        {
            if (a[i] < b[i])
            { //需要借位
                int j = i + 1;
                while (!a[j])
                    j++;
                while (j > i)
                    a[j--]--, a[j] += BASE;
            }
        }
        return a.format();
    }
    friend BigInt operator-(BigInt a, const BigInt &b) { return a -= b; }
    friend BigInt &operator--(BigInt &a) { return a -= 1; }
    friend BigInt operator--(BigInt &a, int)
    {
        BigInt old = a;
        --a;
        return old;
    }

    ///乘法，不能先实现*=，因为是类多项式相乘，每位都需要保留，不能覆盖
    friend BigInt operator*(const BigInt &a, const BigInt &b)
    {
        BigInt n;
        n.SIGN = a.SIGN ^ b.SIGN;
        n.assign(a.size() + b.size() - 1, 0); //表示乘积后最少的位数(可能会被format消掉，因此添加了format参数)
        for (int i = 0; i < a.size(); i++)
        {
            for (int j = 0; j < b.size(); j++)
                n[i + j] += a[i] * b[j];
            n.format(n.size()); //提前进位
        }
        return n; //最后进位可能会溢出
    }
    friend BigInt &operator*=(BigInt &a, const BigInt &b) { return a = a * b; }

    ///带余除法函数，方便除法和模运算，暂时写不出高效的高精与高精的除法
    friend BigInt divmod(BigInt &a, const BigInt &b)
    { // O(logn)，待修改
        assert(b != 0);
        BigInt n;
        if (-MAX_INT - 1 <= b && b <= MAX_INT)
        { //除数小于等于整型才能用这个，不然会溢出
            n = a;
            n.SIGN = a.SIGN ^ b.SIGN;
            long long rest = 0;
            long long bl = 0;
            for (int i = b.size() - 1; i >= 0; i--)
                bl = bl * BASE + b[i];
            for (int i = n.size() - 1; i >= 0; i--)
            {
                rest *= BASE;
                n[i] += rest;
                rest = n[i] % bl;
                n[i] /= bl;
            }
            a = a.SIGN ? (-rest) : rest;
            return n.format(); //必要的不然会有前导0
        }
        else
        {
            n.SIGN = a.SIGN ^ b.SIGN;
            for (int i = a.size() - b.size(); abs(a) >= abs(b); i--)
            { //减法代替除法
                BigInt c, d;
                d.assign(i + 1, 0);
                d.back() = 1;
                d.SIGN = n.SIGN;
                c = b * d; //提高除数位数进行减法
                while (abs(a) >= abs(c))
                    a -= c, n += d;
                d.pop_back();
                if (!d.empty())
                { //遍历压的位
                    d.back() = BASE / 10;
                    for (int i = 1; i < WIDTH; i++)
                    {
                        c = b * d;
                        while (abs(a) >= abs(c))
                            a -= c, n += d;
                        d.back() /= 10;
                    }
                }
            }
            return n;
        }
    }
    friend BigInt operator/(BigInt a, const BigInt &b) { return divmod(a, b); }
    friend BigInt &operator/=(BigInt &a, const BigInt &b) { return a = a / b; }
    friend BigInt &operator%=(BigInt &a, const BigInt &b) { return divmod(a, b), a; }
    friend BigInt operator%(BigInt a, const BigInt &b) { return a %= b; }
};
```

