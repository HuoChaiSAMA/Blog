---
abbrlink: minesweeper
categories:
- - Github
date: '2026-01-10T23:08:12.110968+08:00'
description: null
excerpt: Henry的自制扫雷小游戏。采用c++语言，通过命令行窗口实现。
headimg: https://s2.loli.net/2026/01/10/Rr8DL3UqlaytWJM.png
keywords: null
pin: true
plugins:
- katex
readmore: true
tags:
- cpp
title: Minesweeper.cpp
updated: '2026-02-07T11:03:37.174+08:00'
---
![游戏示例](https://s2.loli.net/2026/01/10/ZwhTuYXj9ApSb7I.png)

源代码仓库如下

{% link Github Repo::https://github.com/HuoChaiSAMA/Minesweeper::https://s2.loli.net/2026/01/10/yu3I6LzSvcptona.png %}

## 前言

* **童年阴影**系列：小学四年级时便有用c++写一个扫雷游戏的想法。但由于当时能力不太足够，刚写完随机地图便接连报错。于是搁置了这个想法。时隔多年，又有了空闲时间，便回想起这个未完成的游戏。于是在2024年12月，在GIthub上建了这个repo，准备完成小时候未竟之事，满足那个遥远的愿望。
* 仓库简介：这是一个仿制**Microsoft**扫雷游戏而编写的游戏，具体玩法与原作相同。由于技术有限，只会一点 `c++`,于是选择了在命令行窗口中呈现的方案。由于命令行窗口无法使用鼠标进行交互，便选择采用**全键盘操作**进行代替。对每一个格子打上坐标，操作时输入对应坐标即可。但每进行一次操作就输入一次坐标显然十分麻烦，于是便使用 `X`作为表示创建了**选框模式**。目前主要玩法是采用选框选择的方式进行游玩。游戏配备了基础的功能，有成功与失败判定，但部分内容还未优化。

## 玩法简介

### 游戏操作

* 使用 {% kbd W %} 、 {% kbd A %} 、 {% kbd S %} 、 {% kbd D %} 对选框进行移动。键盘输入左边快速进行选框移动。
* 使用 {% kbd Z %} 进行方格标记。目前支持三种未知方格状态：无特殊标记 `N`、标记为地雷 `P`、标记为疑问 `?`。其中地雷与疑问标记会导致剩余地雷计数减一（这个逻辑存疑，但没多少人会使用疑问标记😃）
* 使用 {% kbd X %} 进行**安全展开**。此处**安全展开**指原版游戏中的鼠标双键操作，仅对已展开的数字格起效。当该格周围标记数已经符合该格数字时，将自动展开其周围方格，包括未判断出的地雷方格。
* 输入一个**有序实数对**以快速移动选框至目标坐标方格。坐标在游戏界面上已经表示出。与惯常不同的是，界面左侧的纵轴表示$x$坐标，界面上方的横轴表示$y$坐标

{% note info:: 由于没有编写ui的能力，游戏界面只能在命令行窗口以这样的形式呈现。实际操作过程可能较繁琐，待以后优化。 %}

### 胜利/失败条件

#### 胜利条件

正确标记所有地雷或者翻开所有无地雷的方格即为游戏胜利。界面下部分显示了剩余未标记地雷数，但并不表示已标记的方格是正确的。

#### 失败条件

翻开了地雷方格（包括使用安全展开的方格）{% psw 安全展开并不安全 %}

## 源码展示 & 功能拆解

### 源码展示

{% folding green:: 点击查看完整源码 %}

```c++
// #include <bits/stdc++.h>
#include <cstdio>
#include <queue>
#include <windows.h>
// windows.h for Sleep();
#include <iostream>
#include <stdlib.h>
// stdlib.h for system("cls");
// stdlib.h for rand() & srand()
// srand(time(NULL));  // 初始化随机数生成器
// int random_number = rand() % 100;  // 生成 0 到 99 之间的随机数

using namespace std;
// map_size:9x9
//+---+ x---x
//| P | | S |
//+---+ x---X
// x = 1, y = 1; X = 1, Y = 2;
// x = 1, X = 1; x = 2, X = 3; X = x * 2 - 1
// y = 1, Y = 2; y = 2, Y = 6; Y = y * 4 - 2
//     1   2   3   4   5   6   7   8   9
//   0123456789
//  0+---+---+---+---+---+---+---+---+---+ y = 37
// 11|   |   |   |   |   |   |   |   |   |
//  2+---+---+---+---+---+---+---+---+---+
// 23|   |   |   |   |   |   |   |   |   |
//  4+---+---+---+---+---+---+---+---+---+
// 35|   |   |   |   |   |   |   |   |   |
//  6+---+---+---+---+---+---+---+---+---+
// 47|   |   |   |   |   |   |   |   |   |
//  8+---+---+---+---+---+---+---+---+---+
// 59|   |   |   |   |   |   |   |   |   |
//   +---+---+---+---+---+---+---+---+---+
// 6 |   |   |   |   |   |   |   |   |   |
//   +---+---+---+---+---+---+---+---+---+
// 7 |   |   |   |   |   |   |   |   |   |
//   +---+---+---+---+---+---+---+---+---+
// 8 |   |   |   |   |   |   |   |   |   |
//   +---+---+---+---+---+---+---+---+---+
// 9 |   |   |   |   |   |   |   |   |   |
//   +---+---+---+---+---+---+---+---+---+ x = 19
// p(x,y) (x,y [1,9])
int mine_map[13][13];
char display_map[19][37];
int calc_map[13][13];
int special_map[13][13];
int tot = 10, tot_correct = 10;
bool flag = 1;
struct point
{
    int x;
    int y;
} mine[11], selector, p1;
// x = 1, X = 1; x = 2, X = 3; X = x * 2 - 1
// y = 1, Y = 2; y = 2, Y = 6; Y = y * 4 - 2
point locate_point(point p)
{
    p.x = p.x * 2 - 1;
    p.y = p.y * 4 - 2;
    return p;
}
void print_screen()
{
    system("cls");
    cout << "    1   2   3   4   5   6   7   8   9" << endl;
    for (int i = 0; i <= 18; i++)
    {
        if (i % 2 == 1)
        {
            cout << i / 2 + 1 << ' ';
        }
        else
        {
            cout << "  ";
        }
        for (int j = 0; j <= 36; j++)
        {
            cout << display_map[i][j];
        }
        cout << endl;
    }
    cout << "剩余地雷: " << tot << endl;
    cout << "输入以下字符以操控(不区分大小写): " << endl;
    cout << "W A S D : 移动选框" << endl;
    cout << "Z : 标记/问号/取消   X : 安全展开(对数字方格有效)   C : 直接展开" << endl;
    cout << "(num1,num2) : 连续输入两个数字，快速移动选框至目标方格" << endl;
    cout << "请输入：";
    return;
}

void send_error(int style)
{
    // style : 1: 操作字符错误   2：无法操作(所选方格越界)   3: 自动展开操作选择错误
    system("cls");
    if (style == 1)
    {
        cout << "指令错误!" << endl;
        cout << "请使用受支持的指令与格式!";
    }
    else if (style == 2)
    {
        cout << "无法操作!(所选方格不存在或越界)";
    }
    else if (style == 3)
    {
        cout << "无法执行展开!所选方格必须安全、已知且其周围标记的方格数已满足该格数量要求!";
    }
    else if (style == 4)
    {
        cout << "无法标记已经展开的方格!";
    }
    Sleep(2000);
    print_screen();
    return;
}

void draw_selector()
{
    // WRONG!!!
    // display_map[(selector.x - 1) * 4 + 2][selector.y * 2 + 1]
    point s = locate_point(selector);
    display_map[s.x - 1][s.y - 2] = 'X';
    display_map[s.x - 1][s.y + 2] = 'X';
    display_map[s.x + 1][s.y - 2] = 'X';
    display_map[s.x + 1][s.y + 2] = 'X';
}

void erase_selector()
{
    point s = locate_point(selector);
    display_map[s.x - 1][s.y - 2] = '+';
    display_map[s.x - 1][s.y + 2] = '+';
    display_map[s.x + 1][s.y - 2] = '+';
    display_map[s.x + 1][s.y + 2] = '+';
}
queue<point> q;
void endgame()
{
    cout << "    1   2   3   4   5   6   7   8   9" << endl;
    for (int i = 0; i <= 18; i++)
    {
        if (i % 2 == 1)
        {
            cout << i / 2 + 1 << ' ';
        }
        else
        {
            cout << "  ";
        }
        for (int j = 0; j <= 36; j++)
        {
            cout << display_map[i][j];
        }
        cout << endl;
    }
    if (tot == 0)
    {
        cout << "You Did It !!!";
    }
    else
    {
        cout << "Game Over";
    }

    return;
}
void gameover()
{
    system("cls");
    point px, pp;
    // cout << "Game Over";
    for (int i = 1; i <= 9; i++)
    {
        for (int j = 1; j <= 9; j++)
        {
            px.x = i;
            px.y = j;
            pp = locate_point(px);
            if (display_map[pp.x][pp.y] == 'N')
            {

                display_map[pp.x][pp.y] = calc_map[px.x][px.y] + '0';
                if (calc_map[px.x][px.y] == 0)
                {
                    display_map[pp.x][pp.y] = ' ';
                }
                else if (calc_map[px.x][px.y] == 9)
                {
                    display_map[pp.x][pp.y] = '*';
                }
            }
        }
    }
    flag = 0;
    while (!q.empty())
    {
        q.pop();
    }
    endgame();
    return;
}

void bfs_reveal_map()
{
    int delta_x[8] = {-1, -1, -1, 0, 0, 1, 1, 1};
    int delta_y[8] = {-1, 0, 1, -1, 1, -1, 0, 1};
    point p, ps;
    while (!q.empty())
    {
        p = q.front();
        q.pop();
        ps = locate_point(p);
        // if (!mine_map[p.x][p.y])
        // {
        //     display_map[ps.x][ps.y] = ' ';
        // }
        // else
        // {
        //     gameover();
        // }
        display_map[ps.x][ps.y] = calc_map[p.x][p.y] + '0';
        if (calc_map[p.x][p.y] == 0)
        {
            display_map[ps.x][ps.y] = ' ';
        }
        if (display_map[ps.x][ps.y] == ' ')
        {
            for (int i = 0; i < 8; i++)
            {
                if (p.x + delta_x[i] >= 1 && p.x + delta_x[i] <= 9 && p.y + delta_y[i] >= 1 && p.y + delta_y[i] <= 9)
                {
                    point it;
                    it.x = p.x + delta_x[i];
                    it.y = p.y + delta_y[i];
                    point itt = locate_point(it);
                    if (display_map[itt.x][itt.y] == 'N')
                    {
                        q.push(it);
                    }
                }
            }
        }
    }
    print_screen();
    return;
}

void click_to_show(point p)
{
    point pp = locate_point(p);
    if (display_map[pp.x][pp.y] == 'P' || display_map[pp.x][pp.y] == '?')
    {
        send_error(3);
        return;
    }
    if (mine_map[p.x][p.y] == 1)
    {
        gameover();
    }
    else
    {
        q.push(p);
        bfs_reveal_map();
    }
    return;
}

void click_to_help_show(point p)
{
    point pp = locate_point(p);
    point px;
    if (display_map[pp.x][pp.y] == 'N' || display_map[pp.x][pp.y] == ' ')
    {
        send_error(3);
    }
    else
    {
        int tot = 0;
        // int rled = 0;
        int delta_x[8] = {-1, -1, -1, 0, 0, 1, 1, 1};
        int delta_y[8] = {-1, 0, 1, -1, 1, -1, 0, 1};
        for (int j = 0; j <= 7; j++)
        {
            px.x = p.x + delta_x[j];
            px.y = p.y + delta_y[j];
            if (px.x < 1 || px.x > 9 || px.y < 1 || px.y > 9)
            {
                continue;
            }
            pp = locate_point(px);
            if (display_map[pp.x][pp.y] == 'P')
            {
                tot++;
            }
        }
        if (tot == calc_map[p.x][p.y])
        {
            q.push(p);

            for (int j = 0; j <= 7; j++)
            {
                px.x = p.x + delta_x[j];
                px.y = p.y + delta_y[j];
                if (px.x < 1 || px.x > 9 || px.y < 1 || px.y > 9)
                {
                    continue;
                }
                pp = locate_point(px);
                if (display_map[pp.x][pp.y] == 'N')
                {
                    if (mine_map[px.x][px.y] == 1)
                    {
                        gameover();
                        return;
                    }
                    display_map[pp.x][pp.y] = calc_map[px.x][px.y] + '0';
                    if (calc_map[px.x][px.y] == 0)
                    {
                        display_map[pp.x][pp.y] = ' ';
                    }
                }
            }

            bfs_reveal_map();
        }
    }
    return;
}

void read_command()
{
    // cout << "输入以下字符以操控(不区分大小写)：" << endl;
    // cout << "W A S D : 移动选框" << endl;
    // cout << "Z : 标记/问号/取消   X : 安全展开(对数字方格有效)   C : 直接展开" << endl;
    // cout << "(num1,num2) : 连续输入两个数字，快速移动选框至目标方格" << endl;
    // cout << "请输入：";
    char c;
    cin >> c;
    if ((c >= 'a' && c <= 'z') || (c >= 'A' && c <= 'Z'))
    {
        erase_selector();
        if (c >= 'A' && c <= 'Z')
        {
            c -= 'A' - 'a';
        }
        if (c == 'w')
        {
            if (selector.x > 1)
            {
                selector.x -= 1;
            }
            else
            {
                send_error(2);
            }
        }
        else if (c == 'a')
        {
            if (selector.y > 1)
            {
                selector.y -= 1;
            }
            else
            {
                send_error(2);
            }
        }
        else if (c == 's')
        {
            if (selector.x < 9)
            {
                selector.x += 1;
            }
            else
            {
                send_error(2);
            }
        }
        else if (c == 'd')
        {
            if (selector.y < 9)
            {
                selector.y += 1;
            }
            else
            {
                send_error(2);
            }
        }
        else if (c == 'z')
        {
            p1 = locate_point(selector);
            if (display_map[p1.x][p1.y] == 'N' || display_map[p1.x][p1.y] == 'P' || display_map[p1.x][p1.y] == '?')
            {
                if (display_map[p1.x][p1.y] == 'N')
                {
                    // special_map[selector.x][selector.y] = 1;
                    display_map[p1.x][p1.y] = 'P';
                    tot--;
                    if (mine_map[selector.x][selector.y] == 1)
                    {
                        tot_correct--;
                    }
                }
                else if (display_map[p1.x][p1.y] == 'P')
                {
                    // special_map[selector.x][selector.y] = 2;
                    display_map[p1.x][p1.y] = '?';
                }
                else if (display_map[p1.x][p1.y] == '?')
                {
                    // special_map[selector.x][selector.y] = 0;
                    display_map[p1.x][p1.y] = 'N';
                    if (mine_map[selector.x][selector.y] == 1)
                    {
                        tot_correct++;
                    }
                    tot++;
                }
            }
            else
            {
                send_error(4);
            }
        }
        else if (c == 'x')
        {
            click_to_help_show(selector);
        }
        else if (c == 'c')
        {
            click_to_show(selector);
        }
        draw_selector();
    }
    else if (c >= '0' && c <= '9')
    {
        int tx = c - '0';
        int ty;
        cin >> ty;
        erase_selector();
        if (tx >= 1 && tx <= 9 && ty >= 1 && ty <= 9)
        {
            selector.x = tx;
            selector.y = ty;
        }
        else
        {
            send_error(1);
        }
        draw_selector();
    }
    return;
}

void developer_show_map()
{
    cout << "mine_map:" << endl;
    for (int i = 1; i <= 9; i++)
    {
        for (int j = 1; j <= 9; j++)
        {
            cout << mine_map[i][j] << ' ';
        }
        cout << endl;
    }
    cout << "calc_map:" << endl;
    for (int i = 1; i <= 9; i++)
    {
        for (int j = 1; j <= 9; j++)
        {
            cout << calc_map[i][j] << ' ';
        }
        cout << endl;
    }
    cout << "display_map" << endl;
    for (int i = 0; i <= 18; i++)
    {
        for (int j = 0; j <= 36; j++)
        {
            cout << display_map[i][j];
        }
        cout << endl;
    }
    return;
}

void draw_map()
{
    memset(display_map, ' ', sizeof(display_map));
    for (int i = 0; i <= 18; i += 2)
    {
        for (int j = 0; j <= 36; j++)
        {
            if (j % 4 == 0)
            {
                display_map[i][j] = '+';
            }
            else
            {
                display_map[i][j] = '-';
            }
        }
    }
    for (int i = 1; i <= 17; i += 2)
    {
        for (int j = 0; j <= 37; j += 4)
        {
            display_map[i][j] = '|';
        }
    }
    // p(x,y) (x,y [1,9])
    // display( ((x-1)*4)+2 , 2*y-1 )
    point p1, px;
    for (int i = 1; i <= 9; i++)
    {
        for (int j = 1; j <= 9; j++)
        {
            p1.x = i;
            p1.y = j;
            px = locate_point(p1);
            display_map[px.x][px.y] = 'N';
        }
    }

    return;
}

void generate()
{
    memset(mine_map, 0, sizeof(mine_map));
    srand(time(NULL));
    int i = 1;
    int randx, randy;
    bool judge_edge[4]; // 0 == left; 1 == top; 2 == button; 3 == right;
    memset(judge_edge, 0, sizeof(judge_edge));
    int cnt_edge = 0;
    while (i <= 10)
    {
        randx = rand() % 9 + 1;
        randy = rand() % 9 + 1;
        if (mine_map[randx][randy] == 0)
        {
            if (cnt_edge < 4)
            {
                if (randx == 1 && judge_edge[0] == 0)
                {
                    judge_edge[0] = 1;
                    cnt_edge++;
                    mine_map[randx][randy] = 1;
                    mine[i].x = randx;
                    mine[i].y = randy;
                    i++;
                    continue;
                }
                else if (randx == 9 && judge_edge[3] == 0)
                {
                    judge_edge[3] = 1;
                    cnt_edge++;
                    mine_map[randx][randy] = 1;
                    mine[i].x = randx;
                    mine[i].y = randy;
                    i++;
                    continue;
                }
                else if (randy == 1 && judge_edge[1] == 0)
                {
                    judge_edge[1] = 1;
                    cnt_edge++;
                    mine_map[randx][randy] = 1;
                    mine[i].x = randx;
                    mine[i].y = randy;
                    i++;
                    continue;
                }
                else if (randy == 9 && judge_edge[2] == 0)
                {
                    judge_edge[2] = 1;
                    cnt_edge++;
                    mine_map[randx][randy] = 1;
                    mine[i].x = randx;
                    mine[i].y = randy;
                    i++;
                    continue;
                }
            }
            else
            {
                mine_map[randx][randy] = 1;
                mine[i].x = randx;
                mine[i].y = randy;
                i++;
            }
        }
        else
        {
            continue;
        }
    }
    for (int i = 1; i <= 9; i++)
    {
        for (int j = 1; j <= 9; j++)
        {
            if (mine_map[i][j] == 0)
            {
                calc_map[i][j] = mine_map[i][j - 1] + mine_map[i][j + 1] + mine_map[i - 1][j - 1] + mine_map[i - 1][j] + mine_map[i - 1][j + 1] + mine_map[i + 1][j - 1] + mine_map[i + 1][j] + mine_map[i + 1][j + 1];
            }
            else
            {
                calc_map[i][j] = 9;
            }
        }
    }

    return;
}

// p(x,y) (x,y [1,9])
// display( ((x-1)*4)+2 , 2*y-1 )
int main()
{
    generate();
    draw_map();
    selector.x = 1;
    selector.y = 1;
    draw_selector();
    // print_screen();
    flag = 1;
    while (flag)
    {
        system("cls");
        print_screen();
        // developer_show_map();
        read_command();
        if (tot_correct == 0)
        {
            gameover();
        }
    }
    // endgame();
    // read_command();
    // developer_show_map();
    // initialize();
    return 0;
}
```

{% endfolding %}

### 源码拆解

#### 一. 头文件与全局定义

```c++
// #include <bits/stdc++.h>
#include <cstdio>
#include <queue>
#include <windows.h>
// windows.h for Sleep();
#include <iostream>
#include <stdlib.h>
// stdlib.h for system("cls");
// stdlib.h for rand() & srand()
// srand(time(NULL));  // 初始化随机数生成器
// int random_number = rand() % 100;  // 生成 0 到 99 之间的随机数

using namespace std;
```

|    头文件    | 作用                                           |
| :-----------: | :--------------------------------------------- |
| `iostream` | 处理输入输出                                   |
|   `queue`   | STL中的队列数据类型，用于广度优先搜索(BFS)算法 |
| `windows.h` | 用于 `Sleep()` 函数实现延迟                  |
| `stdlib.h` | 用于清屏和生成随机数                           |

{% note warning:: 代码中使用了<windows.h> 以实现等待时间功能。mac用户可能无法运行这个程序 %}

#### 二. 函数结构定义

```c++
// 地图尺寸定义：9x9
// 坐标转换公式说明
// x = 1, X = 1; x = 2, X = 3; X = x * 2 - 1
// y = 1, Y = 2; y = 2, Y = 6; Y = y * 4 - 2

int mine_map[13][13];        // 地雷分布图（1表示有雷）
char display_map[19][37];    // 显示给玩家的地图
int calc_map[13][13];        // 每个格子周围地雷数
int special_map[13][13];     // 特殊状态（当前未使用）

bool flag = 1;               // 游戏进行标志

// 坐标点结构体
struct point {
    int x;
    int y;
} mine[11], selector, p1;    // 地雷数组、选择器、临时点
```

游戏采用二维数组来对地图进行存储:

* `mine_map`: 用于存储地雷位置，`1`代表有雷，`0`代表无雷
* `calc_map`: 用于计算每个方格所显示的数字，代表该方格周围 $9 \times 9$ 区域中地雷个数。地雷方格此处用 `9`代替
* `display_map`: 用于存储呈现给玩家的地图

{% note info:: 游戏地图尺寸为 $9 \times 9$ ，但实际编写时为了防止数组越界，开了 $13 \times 13$ 的二维数组。%}

* `flag`: 用于判断游戏状态。`1`代表游戏正常进行；`0`代表游戏失败
* `point`: 用于存储点信息的结构体，两个变量 `x`、`y`分别代表该点横、纵坐标

#### 三. 核心工具函数

##### 1. 坐标转换函数

```c++
point locate_point(point p) {
    p.x = p.x * 2 - 1;
    p.y = p.y * 4 - 2;
    return p;
}
```

* 将逻辑坐标(点阵坐标)转换为显示坐标（屏幕上的位置）
* 公式：$X = x \times 2 - 1$ , $Y = y \times 4 - 2$

##### 2. 界面绘制函数

```c++
void print_screen()
{
    system("cls");
    cout << "    1   2   3   4   5   6   7   8   9" << endl;
    for (int i = 0; i <= 18; i++) {
        // 行号显示
        if (i % 2 == 1) cout << i / 2 + 1 << ' ';
        else cout << "  ";
  
        // 地图内容显示
        for (int j = 0; j <= 36; j++) {
            cout << display_map[i][j];
        }
        cout << endl;
    }
    // 操作说明
    cout << "剩余地雷: " << tot << endl;
    cout << "输入以下字符以操控(不区分大小写): " << endl;
    // ... 操作说明文本
    cout << "请输入：";
}
```

* 清屏并打印完整的游戏界面
* 显示坐标轴和游戏地图
* 显示剩余地雷数和操作说明

##### 3. 选框绘制函数

```c++
void draw_selector() {
    point s = locate_point(selector);
    display_map[s.x - 1][s.y - 2] = 'X';
    display_map[s.x - 1][s.y + 2] = 'X';
    display_map[s.x + 1][s.y - 2] = 'X';
    display_map[s.x + 1][s.y + 2] = 'X';
}

void erase_selector() {
    point s = locate_point(selector);
    display_map[s.x - 1][s.y - 2] = '+';
    display_map[s.x - 1][s.y + 2] = '+';
    display_map[s.x + 1][s.y - 2] = '+';
    display_map[s.x + 1][s.y + 2] = '+';
}
```

* `draw_selector`: 在选框位置四个角绘制'X'标记
* `erase_selector`: 清除选框标记，恢复为'+'边框
* 实现了可视化的光标效果

{% noteblock :: 选框效果 %}

```c++
+---+        X---X
|   |        |   |
+---+        X---X    
正常方格     选框方格
```

{% endnoteblock %}

#### 四. 游戏逻辑核心函数

##### 1. 地雷生成函数(简化代码示例)

{% folding green:: 点击查看完整源码 %}

```c++
void generate()
{
    memset(mine_map, 0, sizeof(mine_map));
    srand(time(NULL)); //初始化
    int i = 1;
    int randx, randy;
    bool judge_edge[4]; // 0 == left; 1 == top; 2 == button; 3 == right;
    memset(judge_edge, 0, sizeof(judge_edge));
    int cnt_edge = 0;
    while (i <= 10) //随机出10个不重复坐标
    {
        randx = rand() % 9 + 1;
        randy = rand() % 9 + 1;
        if (mine_map[randx][randy] == 0)
        {
            if (cnt_edge < 4)
            {
                if (randx == 1 && judge_edge[0] == 0) //优先保证四个边上有地雷，防止地雷集中在某一小块(可优化)
                {
                    judge_edge[0] = 1;
                    cnt_edge++;
                    mine_map[randx][randy] = 1;
                    mine[i].x = randx;
                    mine[i].y = randy;
                    i++;
                    continue;
                }
                else if (randx == 9 && judge_edge[3] == 0)
                {
                    judge_edge[3] = 1;
                    cnt_edge++;
                    mine_map[randx][randy] = 1;
                    mine[i].x = randx;
                    mine[i].y = randy;
                    i++;
                    continue;
                }
                else if (randy == 1 && judge_edge[1] == 0)
                {
                    judge_edge[1] = 1;
                    cnt_edge++;
                    mine_map[randx][randy] = 1;
                    mine[i].x = randx;
                    mine[i].y = randy;
                    i++;
                    continue;
                }
                else if (randy == 9 && judge_edge[2] == 0)
                {
                    judge_edge[2] = 1;
                    cnt_edge++;
                    mine_map[randx][randy] = 1;
                    mine[i].x = randx;
                    mine[i].y = randy;
                    i++;
                    continue;
                }
            }
            else //四个边均有雷后，完全随机生成
            {
                mine_map[randx][randy] = 1;
                mine[i].x = randx;
                mine[i].y = randy;
                i++;
            }
        }
        else
        {
            continue;
        }
    }
    for (int i = 1; i <= 9; i++)
    {
        for (int j = 1; j <= 9; j++)
        {
            if (mine_map[i][j] == 0) //计算每个格子该显示的数字，方法是把周围格子的值累加(有雷为1没有为0)
            {
                calc_map[i][j] = mine_map[i][j - 1] + mine_map[i][j + 1] + mine_map[i - 1][j - 1] + mine_map[i - 1][j] + mine_map[i - 1][j + 1] + mine_map[i + 1][j - 1] + mine_map[i + 1][j] + mine_map[i + 1][j + 1];
            }
            else
            {
                calc_map[i][j] = 9;
            }
        }
    }

    return;
}
```

{% endfolding %}

```c++
void generate() {
    memset(mine_map, 0, sizeof(mine_map));
    srand(time(NULL));
    // ... 随机生成地雷代码
    // 确保地图四条边各有一个地雷
    // 计算每个格子周围的地雷数
}
```

* 初始化地雷地图
* 使用随机数生成10个地雷位置
* 强制在地图四边各放置一个地雷（特殊要求）
* 计算每个非地雷格子周围的地雷数量

##### 2. 地图绘制函数

```c++
void draw_map() {
    memset(display_map, ' ', sizeof(display_map));
    // 绘制网格边框
    for (int i = 0; i <= 18; i += 2) {
        for (int j = 0; j <= 36; j++) {
            if (j % 4 == 0) display_map[i][j] = '+';
            else display_map[i][j] = '-';
        }
    }
    for (int i = 1; i <= 17; i += 2) {
        for (int j = 0; j <= 37; j += 4) {
            display_map[i][j] = '|';
        }
    }
    // 初始化所有格子为未翻开状态'N'
    for (int i = 1; i <= 9; i++) {
        for (int j = 1; j <= 9; j++) {
            point p1 = {i, j};
            point px = locate_point(p1);
            display_map[px.x][px.y] = 'N';
        }
    }
}
```

* 绘制游戏网格框架
* 初始化所有格子状态为'N'（未翻开）
* 构建字符界面表示

#### 五. 游戏操作处理

##### 1. 错误输入处理函数

```c++
void send_error(int style) {
    system("cls");
    // style 1-4 对应不同的错误类型
    if (style == 1)
    {
        cout << "指令错误!" << endl;
        cout << "请使用受支持的指令与格式!";
    }
    else if (style == 2)
    {
        cout << "无法操作!(所选方格不存在或越界)";
    }
    else if (style == 3)
    {
        cout << "无法执行展开!所选方格必须安全、已知且其周围标记的方格数已满足该格数量要求!";
    }
    else if (style == 4)
    {
        cout << "无法标记已经展开的方格!";
    }
    Sleep(2000);  // 显示错误信息2秒
    print_screen();
}
```

##### 2. 命令处理函数(简化示例)

{% folding::green 点击查看完整源码%}

```c++
void read_command()
{
    // cout << "输入以下字符以操控(不区分大小写)：" << endl;
    // cout << "W A S D : 移动选框" << endl;
    // cout << "Z : 标记/问号/取消   X : 安全展开(对数字方格有效)   C : 直接展开" << endl;
    // cout << "(num1,num2) : 连续输入两个数字，快速移动选框至目标方格" << endl;
    // cout << "请输入：";
    char c;
    cin >> c;
    if ((c >= 'a' && c <= 'z') || (c >= 'A' && c <= 'Z'))
    {
        erase_selector();
        if (c >= 'A' && c <= 'Z')
        {
            c -= 'A' - 'a';
        }
        if (c == 'w')
        {
            if (selector.x > 1)
            {
                selector.x -= 1;
            }
            else
            {
                send_error(2);
            }
        }
        else if (c == 'a')
        {
            if (selector.y > 1)
            {
                selector.y -= 1;
            }
            else
            {
                send_error(2);
            }
        }
        else if (c == 's')
        {
            if (selector.x < 9)
            {
                selector.x += 1;
            }
            else
            {
                send_error(2);
            }
        }
        else if (c == 'd')
        {
            if (selector.y < 9)
            {
                selector.y += 1;
            }
            else
            {
                send_error(2);
            }
        }
        else if (c == 'z')
        {
            p1 = locate_point(selector);
            if (display_map[p1.x][p1.y] == 'N' || display_map[p1.x][p1.y] == 'P' || display_map[p1.x][p1.y] == '?')
            {
                if (display_map[p1.x][p1.y] == 'N')
                {
                    // special_map[selector.x][selector.y] = 1;
                    display_map[p1.x][p1.y] = 'P';
                    tot--;
                    if (mine_map[selector.x][selector.y] == 1)
                    {
                        tot_correct--;
                    }
                }
                else if (display_map[p1.x][p1.y] == 'P')
                {
                    // special_map[selector.x][selector.y] = 2;
                    display_map[p1.x][p1.y] = '?';
                }
                else if (display_map[p1.x][p1.y] == '?')
                {
                    // special_map[selector.x][selector.y] = 0;
                    display_map[p1.x][p1.y] = 'N';
                    if (mine_map[selector.x][selector.y] == 1)
                    {
                        tot_correct++;
                    }
                    tot++;
                }
            }
            else
            {
                send_error(4);
            }
        }
        else if (c == 'x')
        {
            click_to_help_show(selector);
        }
        else if (c == 'c')
        {
            click_to_show(selector);
        }
        draw_selector();
    }
    else if (c >= '0' && c <= '9')
    {
        int tx = c - '0';
        int ty;
        cin >> ty;
        erase_selector();
        if (tx >= 1 && tx <= 9 && ty >= 1 && ty <= 9)
        {
            selector.x = tx;
            selector.y = ty;
        }
        else
        {
            send_error(1);
        }
        draw_selector();
    }
    return;
}
```

{% endfolding %}

```c++
void read_command() 
{
    char c;
    cin >> c;
    if (是字母) 
    {
        处理WASD移动、Z标记、X展开、C点击
    } 
    else if (是数字) 
    {
        处理快速跳转坐标
    }
}
```

* 处理WASD：移动选框
* 处理Z：切换标记状态（N→P→?→N循环）
* 处理X：安全展开（双击操作）
* 处理C：直接点击格子
* 处理数字：快速跳转到指定坐标

#### 六. 游戏核心算法

##### 1. 广度优先搜索(BFS)展开 (简化示例)

```c++
void bfs_reveal_map() {
    int delta_x[8] = {-1, -1, -1, 0, 0, 1, 1, 1};
    int delta_y[8] = {-1, 0, 1, -1, 1, -1, 0, 1};
  
    while (!q.empty()) {
        // 从队列取出格子
        // 显示格子数字（0显示为空格）
        // 如果是空白格，将周围未翻开的格子加入队列
    }
}
```

{% folding green:: 查看完整代码 %}

```c++
void bfs_reveal_map()
{
    int delta_x[8] = {-1, -1, -1, 0, 0, 1, 1, 1};
    int delta_y[8] = {-1, 0, 1, -1, 1, -1, 0, 1};
    point p, ps;
    while (!q.empty())
    {
        p = q.front();
        q.pop();
        ps = locate_point(p);
        // if (!mine_map[p.x][p.y])
        // {
        //     display_map[ps.x][ps.y] = ' ';
        // }
        // else
        // {
        //     gameover();
        // }
        display_map[ps.x][ps.y] = calc_map[p.x][p.y] + '0';
        if (calc_map[p.x][p.y] == 0)
        {
            display_map[ps.x][ps.y] = ' ';
        }
        if (display_map[ps.x][ps.y] == ' ')
        {
            for (int i = 0; i < 8; i++)
            {
                if (p.x + delta_x[i] >= 1 && p.x + delta_x[i] <= 9 && p.y + delta_y[i] >= 1 && p.y + delta_y[i] <= 9)
                {
                    point it;
                    it.x = p.x + delta_x[i];
                    it.y = p.y + delta_y[i];
                    point itt = locate_point(it);
                    if (display_map[itt.x][itt.y] == 'N')
                    {
                        q.push(it);
                    }
                }
            }
        }
    }
    print_screen();
    return;
}
```

{% endfolding %}

* 实现扫雷游戏的核心展开算法
* 当点击空白格时，自动展开所有相邻的空白格
* 使用队列实现广度优先搜索

##### 2. 点击操作函数(简化示例)

{% folding::green 点击查看完整代码 %}

```c++
void click_to_show(point p) //普通单击操作
{
    point pp = locate_point(p);
    if (display_map[pp.x][pp.y] == 'P' || display_map[pp.x][pp.y] == '?') //防止翻开标记格
    {
        send_error(3);
        return;
    }
    if (mine_map[p.x][p.y] == 1) //判断是否踩雷
    {
        gameover();
    }
    else //加入队列展开
    {
        q.push(p);
        bfs_reveal_map();
    }
    return;
}

void click_to_help_show(point p) //双键单击操作
{
    point pp = locate_point(p);
    point px;
    if (display_map[pp.x][pp.y] == 'N' || display_map[pp.x][pp.y] == ' ') //只对数字格起效果
    {
        send_error(3);
    }
    else
    {
        int tot = 0;
        // int rled = 0;
        int delta_x[8] = {-1, -1, -1, 0, 0, 1, 1, 1};
        int delta_y[8] = {-1, 0, 1, -1, 1, -1, 0, 1};
        for (int j = 0; j <= 7; j++) //计算周围已标记方格数量
        {
            px.x = p.x + delta_x[j];
            px.y = p.y + delta_y[j];
            if (px.x < 1 || px.x > 9 || px.y < 1 || px.y > 9)
            {
                continue;
            }
            pp = locate_point(px);
            if (display_map[pp.x][pp.y] == 'P')
            {
                tot++;
            }
        }
        if (tot == calc_map[p.x][p.y]) //满足条件则加入队列展开
        {
            q.push(p);

            for (int j = 0; j <= 7; j++)
            {
                px.x = p.x + delta_x[j];
                px.y = p.y + delta_y[j];
                if (px.x < 1 || px.x > 9 || px.y < 1 || px.y > 9)
                {
                    continue;
                }
                pp = locate_point(px);
                if (display_map[pp.x][pp.y] == 'N')
                {
                    if (mine_map[px.x][px.y] == 1) //如果出现地雷方格(意味着玩家判断错误)就游戏结束
                    {
                        gameover();
                        return;
                    }
                    display_map[pp.x][pp.y] = calc_map[px.x][px.y] + '0'; //展示方格
                    if (calc_map[px.x][px.y] == 0)
                    {
                        display_map[pp.x][pp.y] = ' ';
                    }
                }
            }

            bfs_reveal_map(); //进行队列展开
        }
    }
    return;
}

```

{% endfolding %}

```c++
void click_to_show(point p) {
    // 检查是否已标记
    if (是标记状态) { send_error(3); return; }
  
    // 检查是否踩雷
    if (是地雷) { gameover(); return; }
  
    // 安全则加入队列展开
    q.push(p);
    bfs_reveal_map();
}

void click_to_help_show(point p) {
    // 检查条件：必须是已翻开的数字格
    if (不是数字格) { send_error(3); return; }
  
    // 检查周围标记数量是否匹配
    if (标记数 == 数字值) {
        // 展开周围未标记的格子
        // 如果展开到地雷则游戏结束
    }
}
```

* `click_to_show`: 普通点击，可能踩雷或展开
* `click_to_help_show`: 双击操作（扫雷高级技巧）
* 实现标记计数验证和智能展开

#### 七. 游戏状态管理

##### 1. 游戏结束函数(简化示例)

{% folding::green 点击查看完整示例 %}

```c++
void gameover()
{
    system("cls");
    point px, pp;
    // cout << "Game Over";
    for (int i = 1; i <= 9; i++)
    {
        for (int j = 1; j <= 9; j++)
        {
            px.x = i;
            px.y = j;
            pp = locate_point(px);
            if (display_map[pp.x][pp.y] == 'N') //地图谜底渲染
            {

                display_map[pp.x][pp.y] = calc_map[px.x][px.y] + '0';
                if (calc_map[px.x][px.y] == 0)
                {
                    display_map[pp.x][pp.y] = ' ';
                }
                else if (calc_map[px.x][px.y] == 9)
                {
                    display_map[pp.x][pp.y] = '*';
                }
            }
        }
    }
    flag = 0; //更新游戏状态
    while (!q.empty()) //重置队列
    {
        q.pop();
    }
    endgame();
    return;
}

```

{% endfolding %}

```c++
void gameover() {
    // 显示所有未翻开的格子
    // 地雷显示为'*'
    // 非地雷显示数字
    flag = 0;  // 停止游戏循环
    while (!q.empty()) q.pop();  // 清空队列
}
```

###### 2. 结果展示函数(简化示例)

{% folding::green 点击查看完整代码 %}

```c++
void endgame()
{
    cout << "    1   2   3   4   5   6   7   8   9" << endl; //展示谜底
    for (int i = 0; i <= 18; i++)
    {
        if (i % 2 == 1)
        {
            cout << i / 2 + 1 << ' ';
        }
        else
        {
            cout << "  ";
        }
        for (int j = 0; j <= 36; j++)
        {
            cout << display_map[i][j];
        }
        cout << endl;
    }
    if (tot == 0) //输出结果
    {
        cout << "You Did It !!!";
    }
    else
    {
        cout << "Game Over";
    }

    return;
}

```

{% endfolding %}

```c++
void endgame() {
    // 打印最终地图
    // 根据胜负显示不同信息
    if (tot == 0) cout << "You Did It !!!";
    else cout << "Game Over";
}
```

#### 八. 主函数流程

```c++
int main() {
    generate();      // 生成地雷地图
    draw_map();      // 绘制初始界面
    selector初始化   // 选择器位置
    draw_selector(); // 绘制选择器
  
    flag = 1;
    while (flag) {
        print_screen();    // 显示界面
        read_command();    // 读取并处理命令
        // 检查胜利条件
    }
    endgame();            // 显示最终结果
    return 0;
}
```

## 示例网站

部署中

## 写在后面

**DS老师**挑刺能力一流的 👍 👍👍
