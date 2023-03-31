# Mario城堡历险记

by 张子健

时间限制: 1000 ms

内存限制: 40000 KB

问题描述

  你是一位靠吃蘑菇成长，闻名世界的超级巨星Mario。特征是大鼻子、头戴帽子、身穿背带工作服，还留着胡子，长年担任天任堂的招牌角色。靠着超群的运动神经与勇气，你曾克服了无数困难险阻，拯救了被大魔王巴库囚禁的桃花公主。而这一次，你才真正遇到麻烦了。

  一觉醒来，你和桃花公主被魔王的手下绑架，并被囚禁在了一座巨大城堡里。城堡是一座巨大的迷宫，没有灯火，暗无天日，因此你无法像平时那样，通过极限跑酷和跳远化解难题，逃出生天。幸运的是，凭借多年在地下水管里摸黑工作的经验，你意识到，自己曾经来过这座城堡，而这座城堡的**地图**也早已被你牢记于心。同时，凭借冷静分析和思考，你也正确地猜到了自己当前所处的**起始位置****和朝向，**以及与桃花公主汇合的**终止位置和朝向**。

![img](http://lambdaoj.com/media/ckeditor_uploads/2021/11/29/16381632801.jpg)

**图1. 迷宫示意图**

如上图所示，迷宫是一个**Lx × Ly**个格点划分的**二维平面**，黑色部分代表墙壁，白色部分代表**可通行区域**。对于图1所示的迷宫有Lx=10，Ly=7，Mario的起始位置为（0，0），Mario的终止位置为（9，6）。Mario的头朝向共有4种状态（上、左、下、右），分别用数字可以表示为：

| Mario头朝向 | 上   | 左   | 下   | 右   |
| ----------- | ---- | ---- | ---- | ---- |
| 数字表示    | 1    | 2    | 3    | 4    |

从而，Mario的位姿可以表示为一个长度为3的数组表示**（X轴坐标，Y轴坐标，头朝向）**。例如，对于图1所示的场景下，Mario的起始位姿可以表示（0，0，1），类似地，终止位姿可以表示为（9，6，1）。

  你很清楚，晚一分钟找到桃花公主，公主都可能遭遇危险，因此，你必须采取尽少的动作以尽快到达终止位置，与公主汇合（对城堡迷宫不甚了解的公主将选择在原地等你），而只有当你站在公主面前握紧她的手时，她才会真正安心。

  为了从起始位置到达终止位置，Mario每一次所能采取的**动作**有以下四种：

| Mario动作 | 前进一格 | 前进两格 | 向左转 | 向右转 |
| --------- | -------- | -------- | ------ | ------ |
| 字符表示  | F        | F2       | L      | R      |

应当注意的是，Mario的头朝向决定了你在迷宫里的前进方向。例如，头朝下的时候向前移动一格，如果没有撞墙，将使得当前Y轴坐标+1。

  请你给出Mario到达终止位姿所需要的最小行动次数，并输出一种行动序列。

输入格式

输入的第1行为迷宫的长度Lx （0< Lx <= 1000）

输入的第2行为迷宫的宽度Ly （0< Ly <= 1000）

输入的第3行至第2+Ly行为bool型二维迷宫地图，每行有Lx个元素，其中1代表有墙壁，0代表可通行

输入的倒数第二行为起始位姿，例如对于图1则为（0，0，1）

输入的最后一行为终止位姿，例如对于图1则为（9，6，1）

输出格式

输出的第一行为Mario最少所需行动次数N

第二行至最后一行（N+1行）为每次具体采取的行动

输入样例

```
以图1为例（本行不计为输入）
10
7
0 1 1 1 1 1 1 1 0 0
0 0 0 1 0 0 0 0 0 0
1 1 0 1 0 1 1 0 1 1
1 1 0 1 0 1 1 0 1 1
1 0 0 0 0 1 1 0 1 1
0 0 1 1 1 1 1 0 1 0
0 0 1 1 1 1 1 0 0 0
0 0 1
9 6 1
```

输出样例

```
23
L
L
F
L
F2
R
F
F2
L
F2
L
F
F2
R
F2
F
R
F2
F2
F
L
F2
L
```

提示

\1. 本题能够确保从起始位姿到终止位姿至少存在一条可行路径

\2. 如果存在多个最短行动策略，输出其中一个即可

\3. Mario不喜欢撞墙

\4. 迷宫边界处也是墙壁。例如图1，在（0，0）点上方和左方都默认有墙壁



思路：用BFS搜索。

需注意的点：人不能跨过墙走路。

~~~c++
#include<stdio.h>
#pragma warning(disable:4996)
#define STARTFLAG -10086
#define QUEUELEN 4000000
#define UP 0
#define LEFT 1
#define DOWN 2
#define RIGHT 3

struct point
{
    short x, y, z;
};

struct status
{
    struct point last;
    bool isFound = false;
};

/// <summary>
/// 0:up 
/// 1:left 
/// 2:down 
/// 3:right
/// </summary>
struct status fourdimmap[1000][1000][4];
bool map[1000][1000];
struct point queue[QUEUELEN]; 
struct point path[100000];
int pathlen = 0;

struct point start; 
struct point end;
int head = 0;
int tail = 0;
short Lx;
short Ly;

void BFS()
{
    fourdimmap[start.x][start.y][start.z].last.x = STARTFLAG;
    fourdimmap[start.x][start.y][start.z].last.y = STARTFLAG;
    fourdimmap[start.x][start.y][start.z].last.z = STARTFLAG;
    fourdimmap[start.x][start.y][start.z].isFound = true;
    queue[tail] = start;
    tail++;
    while (!fourdimmap[end.x][end.y][end.z].isFound)
    {
        int iter = (tail - head + QUEUELEN) % QUEUELEN;
        int tempHead = head;
        for (int i = 0; i < iter; i++)
        {
            int j = (tempHead + i) % QUEUELEN;
            switch (queue[j].z)
            {			
            case UP:
                if (queue[j].y >= 2)
                {
                    if (!map[queue[j].x][queue[j].y - 1])
                    {
                        if (!fourdimmap[queue[j].x][queue[j].y - 1][UP].isFound)
                        {
                            fourdimmap[queue[j].x][queue[j].y - 1][UP].isFound = true;
                            fourdimmap[queue[j].x][queue[j].y - 1][UP].last = queue[j];
                            queue[tail].x = queue[j].x;
                            queue[tail].y = queue[j].y - 1;
                            queue[tail].z = UP;
                            tail++;
                            if (tail >= QUEUELEN)
                                tail = 0;
                        }
                        if (!map[queue[j].x][queue[j].y - 2])
                        {
                            if (!fourdimmap[queue[j].x][queue[j].y - 2][UP].isFound)
                            {
                                fourdimmap[queue[j].x][queue[j].y - 2][UP].isFound = true;
                                fourdimmap[queue[j].x][queue[j].y - 2][UP].last = queue[j];
                                queue[tail].x = queue[j].x;
                                queue[tail].y = queue[j].y - 2;
                                queue[tail].z = UP;
                                tail++;
                                if (tail >= QUEUELEN)
                                    tail = 0;
                            }
                        }
                    }
                    
                }
                else if (queue[j].y == 1)
                {
                    if (!fourdimmap[queue[j].x][queue[j].y - 1][UP].isFound)
                    {
                        fourdimmap[queue[j].x][queue[j].y - 1][UP].isFound = true;
                        fourdimmap[queue[j].x][queue[j].y - 1][UP].last = queue[j];
                        queue[tail].x = queue[j].x;
                        queue[tail].y = queue[j].y - 1;
                        queue[tail].z = UP;
                        tail++;
                        if (tail >= QUEUELEN)
                            tail = 0;
                    }
                }
                if (!fourdimmap[queue[j].x][queue[j].y][LEFT].isFound)
                {
                    fourdimmap[queue[j].x][queue[j].y][LEFT].isFound = true;
                    fourdimmap[queue[j].x][queue[j].y][LEFT].last = queue[j];
                    queue[tail].x = queue[j].x;
                    queue[tail].y = queue[j].y;
                    queue[tail].z = LEFT;
                    tail++;
                    if (tail >= QUEUELEN)
                        tail = 0;
                }
                if (!fourdimmap[queue[j].x][queue[j].y][RIGHT].isFound)
                {
                    fourdimmap[queue[j].x][queue[j].y][RIGHT].isFound = true;
                    fourdimmap[queue[j].x][queue[j].y][RIGHT].last = queue[j];
                    queue[tail].x = queue[j].x;
                    queue[tail].y = queue[j].y;
                    queue[tail].z = RIGHT;
                    tail++;
                    if (tail >= QUEUELEN)
                        tail = 0;
                }
                
                head++;
                if (head >= QUEUELEN)
                    head = 0;
                break;

            case LEFT:
                if (queue[j].x >= 2)
                {
                    if (!map[queue[j].x - 1][queue[j].y])
                    {
                        if (!fourdimmap[queue[j].x - 1][queue[j].y][LEFT].isFound)
                        {
                            fourdimmap[queue[j].x - 1][queue[j].y][LEFT].isFound = true;
                            fourdimmap[queue[j].x - 1][queue[j].y][LEFT].last = queue[j];
                            queue[tail].x = queue[j].x - 1;
                            queue[tail].y = queue[j].y;
                            queue[tail].z = LEFT;
                            tail++;
                            if (tail >= QUEUELEN)
                                tail = 0;
                        }
                        if (!map[queue[j].x - 2][queue[j].y])
                        {
                            if (!fourdimmap[queue[j].x - 2][queue[j].y][LEFT].isFound)
                            {
                                fourdimmap[queue[j].x - 2][queue[j].y][LEFT].isFound = true;
                                fourdimmap[queue[j].x - 2][queue[j].y][LEFT].last = queue[j];
                                queue[tail].x = queue[j].x - 2;
                                queue[tail].y = queue[j].y;
                                queue[tail].z = LEFT;
                                tail++;
                                if (tail >= QUEUELEN)
                                    tail = 0;
                            }
                        }
                    }
                    
                }
                else if (queue[j].x == 1)
                {
                    if (!fourdimmap[queue[j].x - 1][queue[j].y][LEFT].isFound)
                    {
                        fourdimmap[queue[j].x - 1][queue[j].y][LEFT].isFound = true;
                        fourdimmap[queue[j].x - 1][queue[j].y][LEFT].last = queue[j];
                        queue[tail].x = queue[j].x - 1;
                        queue[tail].y = queue[j].y;
                        queue[tail].z = LEFT;
                        tail++;
                        if (tail >= QUEUELEN)
                            tail = 0;
                    }
                }
                if (!fourdimmap[queue[j].x][queue[j].y][DOWN].isFound)
                {
                    fourdimmap[queue[j].x][queue[j].y][DOWN].isFound = true;
                    fourdimmap[queue[j].x][queue[j].y][DOWN].last = queue[j];
                    queue[tail].x = queue[j].x;
                    queue[tail].y = queue[j].y;
                    queue[tail].z = DOWN;
                    tail++;
                    if (tail >= QUEUELEN)
                        tail = 0;
                }
                if (!fourdimmap[queue[j].x][queue[j].y][UP].isFound)
                {
                    fourdimmap[queue[j].x][queue[j].y][UP].isFound = true;
                    fourdimmap[queue[j].x][queue[j].y][UP].last = queue[j];
                    queue[tail].x = queue[j].x;
                    queue[tail].y = queue[j].y;
                    queue[tail].z = UP;
                    tail++;
                    if (tail >= QUEUELEN)
                        tail = 0;
                }
                
                head++;
                if (head >= QUEUELEN)
                    head = 0;
                break;

            case DOWN:
                if (queue[j].y < Ly - 2)
                {
                    if (!map[queue[j].x][queue[j].y + 1])
                    {
                        if (!fourdimmap[queue[j].x][queue[j].y + 1][DOWN].isFound)
                        {
                            fourdimmap[queue[j].x][queue[j].y + 1][DOWN].isFound = true;
                            fourdimmap[queue[j].x][queue[j].y + 1][DOWN].last = queue[j];
                            queue[tail].x = queue[j].x;
                            queue[tail].y = queue[j].y + 1;
                            queue[tail].z = DOWN;
                            tail++;
                            if (tail >= QUEUELEN)
                                tail = 0;
                        }
                        if (!map[queue[j].x][queue[j].y + 2])
                        {
                            if (!fourdimmap[queue[j].x][queue[j].y + 2][DOWN].isFound)
                            {
                                fourdimmap[queue[j].x][queue[j].y + 2][DOWN].isFound = true;
                                fourdimmap[queue[j].x][queue[j].y + 2][DOWN].last = queue[j];
                                queue[tail].x = queue[j].x;
                                queue[tail].y = queue[j].y + 2;
                                queue[tail].z = DOWN;
                                tail++;
                                if (tail >= QUEUELEN)
                                    tail = 0;
                            }
                        }
                    }
                }
                else if (queue[j].y == Ly - 2)
                {
                    if (!fourdimmap[queue[j].x][queue[j].y + 1][DOWN].isFound)
                    {
                        fourdimmap[queue[j].x][queue[j].y + 1][DOWN].isFound = true;
                        fourdimmap[queue[j].x][queue[j].y + 1][DOWN].last = queue[j];
                        queue[tail].x = queue[j].x;
                        queue[tail].y = queue[j].y + 1;
                        queue[tail].z = DOWN;
                        tail++;
                        if (tail >= QUEUELEN)
                            tail = 0;
                    }
                }
                if (!fourdimmap[queue[j].x][queue[j].y][LEFT].isFound)
                {
                    fourdimmap[queue[j].x][queue[j].y][LEFT].isFound = true;
                    fourdimmap[queue[j].x][queue[j].y][LEFT].last = queue[j];
                    queue[tail].x = queue[j].x;
                    queue[tail].y = queue[j].y;
                    queue[tail].z = LEFT;
                    tail++;
                    if (tail >= QUEUELEN)
                        tail = 0;
                }
                if (!fourdimmap[queue[j].x][queue[j].y][RIGHT].isFound)
                {
                    fourdimmap[queue[j].x][queue[j].y][RIGHT].isFound = true;
                    fourdimmap[queue[j].x][queue[j].y][RIGHT].last = queue[j];
                    queue[tail].x = queue[j].x;
                    queue[tail].y = queue[j].y;
                    queue[tail].z = RIGHT;
                    tail++;
                    if (tail >= QUEUELEN)
                        tail = 0;
                }
                
                head++;
                if (head >= QUEUELEN)
                    head = 0;
                break;

            case RIGHT:
                if (queue[j].x < Lx - 2)
                {
                    if (!map[queue[j].x + 1][queue[j].y])
                    {
                        if (!fourdimmap[queue[j].x + 1][queue[j].y][RIGHT].isFound)
                        {
                            fourdimmap[queue[j].x + 1][queue[j].y][RIGHT].isFound = true;
                            fourdimmap[queue[j].x + 1][queue[j].y][RIGHT].last = queue[j];
                            queue[tail].x = queue[j].x + 1;
                            queue[tail].y = queue[j].y;
                            queue[tail].z = RIGHT;
                            tail++;
                            if (tail >= QUEUELEN)
                                tail = 0;
                        }
                        if (!map[queue[j].x + 2][queue[j].y])
                        {
                            if (!fourdimmap[queue[j].x + 2][queue[j].y][RIGHT].isFound)
                            {
                                fourdimmap[queue[j].x + 2][queue[j].y][RIGHT].isFound = true;
                                fourdimmap[queue[j].x + 2][queue[j].y][RIGHT].last = queue[j];
                                queue[tail].x = queue[j].x + 2;
                                queue[tail].y = queue[j].y;
                                queue[tail].z = RIGHT;
                                tail++;
                                if (tail >= QUEUELEN)
                                    tail = 0;
                            }
                        }
                    }
                }
                else if (queue[j].x == Lx - 2)
                {
                    if (!fourdimmap[queue[j].x + 1][queue[j].y][RIGHT].isFound)
                    {
                        fourdimmap[queue[j].x + 1][queue[j].y][RIGHT].isFound = true;
                        fourdimmap[queue[j].x + 1][queue[j].y][RIGHT].last = queue[j];
                        queue[tail].x = queue[j].x + 1;
                        queue[tail].y = queue[j].y;
                        queue[tail].z = RIGHT;
                        tail++;
                        if (tail >= QUEUELEN)
                            tail = 0;
                    }
                }
                if (!fourdimmap[queue[j].x][queue[j].y][DOWN].isFound)
                {
                    fourdimmap[queue[j].x][queue[j].y][DOWN].isFound = true;
                    fourdimmap[queue[j].x][queue[j].y][DOWN].last = queue[j];
                    queue[tail].x = queue[j].x;
                    queue[tail].y = queue[j].y;
                    queue[tail].z = DOWN;
                    tail++;
                    if (tail >= QUEUELEN)
                        tail = 0;
                }
                if (!fourdimmap[queue[j].x][queue[j].y][UP].isFound)
                {
                    fourdimmap[queue[j].x][queue[j].y][UP].isFound = true;
                    fourdimmap[queue[j].x][queue[j].y][UP].last = queue[j];
                    queue[tail].x = queue[j].x;
                    queue[tail].y = queue[j].y;
                    queue[tail].z = UP;
                    tail++;
                    if (tail >= QUEUELEN)
                        tail = 0;
                }
                

                head++;
                if (head >= QUEUELEN)
                    head = 0;
                break;
            }
        }
    }
}

void FindPath()
{
    path[pathlen] = end;
    while (path[pathlen].x != STARTFLAG)  //放进去的最后一个是假的
    {
        path[pathlen + 1] = fourdimmap[path[pathlen].x][path[pathlen].y][path[pathlen].z].last;
        pathlen++;
    }
}

void Display()
{
    printf("%d\n", pathlen - 1);  //经过pathlen个点，只用走pathlen-1次
    for (int i = pathlen - 1; i >= 1; i--)
    {
        if (path[i].x != path[i - 1].x || path[i].y != path[i - 1].y)
        {
            if (path[i].x - path[i - 1].x == 1 || path[i].x - path[i - 1].x == -1 || path[i].y - path[i - 1].y == 1 || path[i].y - path[i - 1].y == -1)
            {
                printf("F\n");
            }
            else if (path[i].x - path[i - 1].x == 2 || path[i].x - path[i - 1].x == -2 || path[i].y - path[i - 1].y == 2 || path[i].y - path[i - 1].y == -2)
            {
                printf("F2\n");
            }
        }
        else
        {
            if (path[i - 1].z == UP)
            {
                if (path[i].z == RIGHT)
                    printf("L\n");
                else printf("R\n");
            }
            else if (path[i - 1].z == DOWN)
            {
                if (path[i].z == LEFT)
                    printf("L\n");
                else printf("R\n");
            }
            else if (path[i - 1].z == LEFT)
            {
                if (path[i].z == UP)
                    printf("L\n");
                else printf("R\n");
            }
            else 
            {
                if (path[i].z == DOWN)
                    printf("L\n");
                else printf("R\n");
            }
        }
    }
}

int main()
{
    scanf("%hd%hd", &Lx, &Ly);
    int k;
    for (int i = 0; i < Ly; i++)
    {
        for (int j = 0; j < Lx; j++)
        {
            scanf("%d", &k);
            map[j][i] = (k == 1);
        }
    }
    scanf("%hd%hd%hd", &start.x, &start.y, &start.z);
    start.z--;
    scanf("%hd%hd%hd", &end.x, &end.y, &end.z);
    end.z--;

    BFS();
    FindPath();
    Display();
    return 0;
}
~~~

