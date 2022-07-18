---
title: 大整数模拟
commentable: flase
Edit: 2022-07-18
mathjax: true
mermaid: true
tags: oi 技巧
categories: code  
description: 大整数模拟的基本思路与实现.
---

# 前言
记录下大整数模拟的快速实现，只支持正整数，负数情况加个函数对符号进行处理。
* 基本思路：string读输入，处理符号，转vector(比单纯的int转char更方便) 

# 比较大小
```cpp
bool cmp(vector<int>& A,vector<int>& B)
{
    if(A.size()!=B.size()) return A.size()>B.size();
    for(int i=A.size()-1;i>=0;i--)
    {
        if(A[i]!=B[i])
        {
            return A[i]>B[i];
        }
    }
    return true;
}
```

# 加法
```cpp
vector<int> Add(vector<int>& A,vector<int>& B)
{
    vector<int> c;
    int t=0;
    for(int i=0;i<A.size()||i<B.size();i++)
    {
        if(i<A.size()) t+=A[i];
        if(i<B.size()) t+=B[i];
        c.push_back(t%10);
        t/=10;
    }
    if(t)  c.push_back(1);
    
    return c;
}
```

# 减法
```cpp
vector<int> sub(vector<int>& A,vector<int>& B)
{
    vector<int> c;
    for(int i=0,t=0;i<A.size();i++)
    {
        t=A[i]-t;
        if(i<B.size()) t-=B[i];
        c.push_back((t+10)%10);
        if(t<0) t=1;
        else t=0;
    }
    while(c.size()>1&&c.back()==0) c.pop_back();
    return c;
}
```
# 乘法
```cpp
vector<int> mul(vector<int>& A,int b)
{
    vector<int> c;
    int t=0;
    for(int i=0;i<A.size()||t;i++)
    {
        if(i<A.size()) t+=A[i]*b;
        c.push_back(t%10);
        t/=10;
    }
      while (c.size() > 1 && c.back() == 0) c.pop_back();
    return c;
}
```

# 除法
```cpp
vector<int> div(vector<int> &A, int b, int &r)
{
    vector<int> C;
    r = 0;
    for (int i = A.size() - 1; i >= 0; i -- )
    {
        r = r * 10 + A[i];
        C.push_back(r / b);
        r %= b;
    }
    reverse(C.begin(), C.end());
    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}
```

# Reference
1.[*算法笔记(四）——大整数运算(附带模板)*](https://blog.csdn.net/m0_58367586/article/details/124539003)