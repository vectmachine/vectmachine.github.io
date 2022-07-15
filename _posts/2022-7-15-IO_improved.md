---
title: IO优化：快读与快写
commentable: flase
Edit: 2022-07-15
mathjax: true
mermaid: true
tags: oi 技巧
categories: code  
description: 快读与快写的基本原理与实现.
---

# 前言
众所周知，std::cout与std::cin特别慢，在卡常数的时候不妨考虑优化下IO（，本文介绍了常见快读与快写的基本原理和实现方式

# baseline 
~~用printf和cin就完了~~

# 关闭同步
输入输出流慢的原因之一是与printf、scanf同步绑定，解绑后能快一点，就是不能同时使用两种方式了
实现：
```cpp
std::ios::sync_with_stdio(false)
```

# 关闭接触绑定（慎用）
另一个导致其效率低的原因是std::cout与std::cin之间的绑定，tie 是将两个 stream 绑定的函数，空参数的话返回当前的输出流指针。
* *在默认的情况下 std::cin 绑定的是 std::cout，每次执行 << 操作符的时候都要调用 flush() 来清理 stream buffer，这样会增加 IO 负担。
可以通过 std::cin.tie(0)（0 表示 NULL）来解除 std::cin 与 std::cout 的绑定，进一步加快执行效率。
但需要注意的是，在解除了 std::cin 和 std::cout 的绑定后，程序中必须手动 flush 才能确保每次 std::cout 展现的内容可以在 std::cin 前出现。这是因为 std::cout 被 buffer 为默认设置。*

# getchar快读快写
将数字处理为字符IO，比printf和cin快
## 读入优化
读入 num 可写为 num=read();
```cpp
int read() 
{
    int x = 0, w = 1;
    char ch = 0;
    while (ch < '0' || ch > '9') 
    {  
        // ch 不是数字时
        if (ch == '-') w = -1;        // 判断是否为负
        ch = getchar();               // 继续读入
    }
    while (ch >= '0' && ch <= '9') 
    {  
        // ch 是数字时
        x = x * 10 + (ch - '0');  // 将新读入的数字’加’在 x 的后面
        // x 是 int 类型，char 类型的 ch 和 ’0’ 会被自动转为其对应的
        // ASCII 码，相当于将 ch 转化为对应数字
        // 此处也可以使用 (x<<3)+(x<<1) 的写法来代替 x*10
        ch = getchar();  // 继续读入
    }
    return x * w;  // 数字 * 正负号 = 实际数值
}
```

## 输出优化
输出 num 可写为 write(num);
```cpp
inline void write(int x) 
{
    static int sta[35];
    int top = 0;
    do 
    {
        sta[top++] = x % 10, x /= 10;
    } 
    while (x);
    while (top) putchar(sta[--top] + 48);  // 48 是 '0'
}

```

# 还能更快的IO？
fread和fwrite支持将文件读入整个IO Buffer，而getchar从文件中读入一个char
那么加上fread和fwrite后，我们只需要修改getchar，从buffer中读char就行
再封装成Template，支持多类型IO
* *使用说明：
关闭调试开关时使用 fread(),fwrite()，退出时自动析构执行 fwrite()。
开启调试开关时使用 getchar(),putchar()，便于调试。
若要开启文件读写时，请在所有读写之前加入 freopen()。*
```cpp
// #define DEBUG 1  // 调试开关
struct IO 
{
#define MAXSIZE (1 << 20)
#define isdigit(x) (x >= '0' && x <= '9')
  char buf[MAXSIZE], *p1, *p2;
  char pbuf[MAXSIZE], *pp;
#if DEBUG
#else
  IO() : p1(buf), p2(buf), pp(pbuf) {}

  ~IO() { fwrite(pbuf, 1, pp - pbuf, stdout); }
#endif
  inline char gc() 
  {
#if DEBUG  // 调试，可显示字符
    return getchar();
#endif
    if (p1 == p2) p2 = (p1 = buf) + fread(buf, 1, MAXSIZE, stdin);
    return p1 == p2 ? ' ' : *p1++;
  }

  inline bool blank(char ch) 
  {
    return ch == ' ' || ch == '\n' || ch == '\r' || ch == '\t';
  }

  template <class T>
  inline void read(T &x) 
  {
    register double tmp = 1;
    register bool sign = 0;
    x = 0;
    register char ch = gc();
    for (; !isdigit(ch); ch = gc())
      if (ch == '-') sign = 1;
    for (; isdigit(ch); ch = gc()) x = x * 10 + (ch - '0');
    if (ch == '.')
      for (ch = gc(); isdigit(ch); ch = gc())
        tmp /= 10.0, x += tmp * (ch - '0');
    if (sign) x = -x;
  }

  inline void read(char *s) 
  {
    register char ch = gc();
    for (; blank(ch); ch = gc());
    for (; !blank(ch); ch = gc()) *s++ = ch;
    *s = 0;
  }

  inline void read(char &c) 
  {
    for (c = gc(); blank(c); c = gc());
  }

  inline void push(const char &c) 
  {
#if DEBUG  // 调试，可显示字符
    putchar(c);
#else
    if (pp - pbuf == MAXSIZE) fwrite(pbuf, 1, MAXSIZE, stdout), pp = pbuf;
    *pp++ = c;
#endif
  }

  template <class T>
  inline void write(T x) 
  {
    if (x < 0) x = -x, push('-');  // 负数输出
    static T sta[35];
    T top = 0;
    do 
    {
      sta[top++] = x % 10, x /= 10;
    } while (x);
    while (top) push(sta[--top] + '0');
  }

  template <class T>
  inline void write(T x, char lastChar) 
  {
    write(x), push(lastChar);
  }
} io;
```

# Reference
1.[*oi-wiki*](https://oi-wiki.org/contest/io/#_9)
