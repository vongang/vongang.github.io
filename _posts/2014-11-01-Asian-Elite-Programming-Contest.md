---
title: 中日精英对抗赛题解
layout: post
category: algorithm
---

# Asian Elite Programming Contest


## 中日精英对抗赛题解
- 别问我这个比赛是干啥的，我也不知道。= =。前段时间一个学长说推荐人参加这个比赛会有推荐费，我只是帮帮忙打打酱油，充个人数。
- 这野鸡比赛上次没玩够，说参加的人太少，又搞了一下。。。好吧，无语了。忙帮帮到底，上次的题我都已经忘了，谢谢这次的题解把
- 发现现在脑子不够用，在这学校被一群牛逼的分分种SCI的人鄙视，压力好大。好久没刷题，今天这比赛做的，只能用搓来形容。
- 下面是题解：

### A 
    二货题

### B
    二货题

### C
    问给S1，S2，S3三个由大写字母组成的字符串，这三个串长度相同，都为2N。问从S1里抽出任意N个字符，从S2里抽出任意N个字符，抽出的这2N个字符能否组成S3串。输出YES or NO
    解：
        如果这题要求构造结果，貌似不容易。但现在是判定性问题，就好处理多了。讨论几种不合法的情况，剩下的就是合法的了。
        不合法情况1：S3里某个字符出现次数比S1和S2中出现次数的和还多。
        不合法情况2：S1对S3可贡献的字符总数小于N。
        不合法情况3：S3对S3可贡献的字符总数小于N。
    代码：

```cpp
#include <iostream>
#include <stdio.h>
#include <cstdlib>
#include <cstring>
#include <string>
 
using namespace std;
 
int cnta[30];
int cntb[30];
int cntc[30];
 
int main() {
    string a, b, c;
    cin >> a >> b >> c;
    memset(cnta, 0, sizeof(cnta));
    memset(cntb, 0, sizeof(cntb));
    memset(cntc, 0, sizeof(cntc));
    int i, n = a.size();
    for(i = 0; i < n; ++i) {
        cnta[a[i]-'A']++;
        cntb[b[i]-'A']++;
        cntc[c[i]-'A']++;
    }
    bool ans = true;
    for(i = 0; i < 26 && ans; ++i) {
        if(cntc[i] > cnta[i] + cntb[i]) ans = false;
    }
    int numa = 0, numb = 0;
    for(i = 0; i < 26 && ans; ++i) {
        if(cntc[i]) {
            numa += min(cnta[i], cntc[i]);
        }
    }
    if(numa < n/2) ans = false;
    for(i = 0; i < 26 && ans; ++i) {
        if(cntc[i]) {
            numb += min(cntb[i], cntc[i]);
        }
    }
    if(numb < n/2)  ans = false;
    //printf("%d %d\n", numa, numb);
    if(ans) cout << "YES" << endl;
    else    cout << "NO" << endl;
 
    return 0;
}

```

### D
    Dave is a mountaineer. He is now climbing a range of mountains.

    On this mountains, there are N huts located on a straight lining from east to west..
    
    The huts are numbered sequentially from 1 to N. The west most hut is 1, the east most hut is N. The i-th hut is located at an elevation of hi meters.
    
    Dave wants to know how many huts he can look down and see from each hut.
    
    He can see the j-th hut from the i-th hut if all huts between the i-th hut and the j-th hut including the j-th one are located at equal or lower elevation than hi.
    
    Note that the i-th hut itself is not included in the hut he can see from the i-th hut.


    给N个高度$h[0...n-1]$, 起跟h[i]相邻比h[i]小的连续高度的个数。不如 1 2 3 2 1， 则存在4个比高度值为3低的高度值。
    
    解：
        这就是一个赤裸裸的栈。。。。我还二货的看了好长时间。。。智商不够用。。。
```cpp
#include <iostream>
#include <stdio.h>
#include <cstdlib>
#include <cstring>
#include <string>
#include <stack>
 
using namespace std;
 
stack< pair<int, int> > st;
 
const int LEN = 100010;
 
int h[LEN];
int cnt1[LEN];
int cnt2[LEN];
 
int main() {
    //freopen("data.in", "r", stdin);
    int n, i;
    scanf("%d", &n);
    for(i = 0; i < n; ++i) {
        scanf("%d", &h[i]);
    }
    while(!st.empty())  st.pop();
    memset(cnt1, 0, sizeof(cnt1));
    memset(cnt2, 0, sizeof(cnt2));
 
    st.push(make_pair(0, h[0]));
 
    for(i = 1; i < n; ++i) {
        while(!st.empty() && st.top().second <= h[i]) {
            st.pop();
        }
        if(st.empty())  cnt1[i] = i;
        else    cnt1[i] = i - st.top().first - 1;
        //printf("<<<<%d %d\n", i, cnt1[i]);
        st.push(make_pair(i, h[i]));
    }
 
    while(!st.empty())  st.pop();
    st.push(make_pair(n - 1, h[n-1]));
 
    for(i = n - 2; i >= 0; --i) {
        while(!st.empty() && st.top().second <= h[i]) {
            st.pop();
        }
        if(st.empty())  cnt2[i] = n - 1 - i;
        else    cnt2[i] = st.top().first - i - 1;
        //printf(">>>>%d %d\n", i, cnt1[i]);
        st.push(make_pair(i, h[i]));
    }
 
    for(i = 0; i < n; ++i) {
        printf("%d\n", cnt1[i] + cnt2[i]);
    }
 
    return 0;
}
```


- 做完这野鸡比赛暴露了好多问题，好久不刷题完全是智商退化。。。以后尽量抽时间刷刷BestCoder吧。。。不能这么搓下去！
