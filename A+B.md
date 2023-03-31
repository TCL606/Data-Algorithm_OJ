# A + B Problem

by Miku Chan

时间限制: 1000 ms

内存限制: 3000 KB

问题描述

输入两个正整数a和b，求 a + b 的值

输入格式

两个正整数 a 和 b。

输出格式

一个正整数。

输入样例

```
1 1
```

输出样例

```
2
```

提示

注意数据范围

代码：

~~~c++
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#pragma warning(disable:4996)
#define dlen 120
int main()
{
    char* a = (char*)malloc(sizeof(char) * dlen);
    char* b = (char*)malloc(sizeof(char) * dlen);
    int add = 0, length;
    scanf("%s%s", a, b);
    length = strlen(a) > strlen(b) ? strlen(a) : strlen(b);
    char* aa = (char*)malloc(sizeof(char) * (length + 1));
    char* bb = (char*)malloc(sizeof(char) * (length + 1));
    if (strlen(a) > strlen(b))
    {
        strcpy(aa, a);
        aa[strlen(a)] = 0;
        strcpy(bb + strlen(a) - strlen(b), b);
        bb[strlen(a)] = 0;
        for (int i = 0; i < strlen(a) - strlen(b); i++)
            bb[i] = '0';
    }
    else
    {
        strcpy(bb, b);
        bb[strlen(b)] = 0;
        strcpy(aa + strlen(b) - strlen(a), a);
        aa[strlen(b)] = 0;
        for (int i = 0; i < strlen(b) - strlen(a); i++)
            aa[i] = '0';
    }
    free(a);
    free(b);
    char* c = (char*)malloc(sizeof(char) * (length + 2));
    c[0] = c[length + 1] = 0;    
    for (int i = length - 1; i >= 0; i--)
    {
        int sum = aa[i] - '0' + bb[i] - '0' + add;
        add = sum >= 10 ? 1 : 0;
        c[i + 1] = sum % 10 + '0';
    }
    if (add)
        c[0] = add + '0';
    else
    {
        for (int i = 0; i < length; i++)
            c[i] = c[i + 1];
        c[length] = 0;
    }
    printf("%s", c);
    free(c);
    free(bb);
    free(aa);
    return 0;
}
~~~

