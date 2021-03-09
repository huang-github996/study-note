title: leetcode-5.最长回文子串-manacher
author: '[dogh](huanghaolin96@foxmail.com)'
tags:
  - manacher
  - string
  - algorithm
categories:
  - leetcode
  - algorithm
date: 2021-01-28 22:55:00
---
## Question：给你一个字符串 s，找到 s 中最长的回文子串。
[leetcode-5](https://leetcode-cn.com/problems/longest-palindromic-substring/)

该题使用马拉车算法解时间复杂度和空间复杂度均为$O(n)$


## manacher思想介绍：
一、(center)	$C$ *为回文串对称中心坐标*
二、(radius) 	$R[C]$  *以C为对称中心的回文串半径*
三、(iterator)   $I$ *对称中心在R[C]范围内，且$I>C$的回文串对称中心坐标*
四、(Point)	    $P$*以C为中心，回文串结束的下标*

下面假设有一个字符串，$i`为i的对称点$
此时$R[i]有两种情况，R[i] \le P 或 R[i] \gt P$
![马拉车情况分析](https://img-blog.csdnimg.cn/20210127234238530.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MDQ3NDYx,size_16,color_FFFFFF,t_70)

情况一： 由于$i`$是已经遍历过的位置，所以存在 $R[i`] \gt0 \&\&R[i`] == R[i]$ ，但此时p点之后的情况是未知的，故将R[i]赋值为$min(R[i`],P-i)$,为的是保证$i + R[i] \le P$
情况二： 由于$i + R[i] \gt P$, 故将$C$点状态转移到$i$点。

分析完马拉车的两种情况之后可以得出代码套路：
1. 将字符串进行处理，方便统计
2. 对字符串进行遍历，利用对称关系和辅助数组，遍历一遍即可得到最长回文子串的对称中心所在。
3. 然后将答案处理至答案数组。	

STEP1：	由于字符串长度分奇偶，首先将字符串处理，在字符串中加入一个没有的字符， 如 ==‘#’==，处理后的字符串性质无改变，只是将奇偶情况合并为一种情况，得到辅助字符串。如 ：
 
	原字符串回文串为==偶数== abbad -> #a#b#b#a#d#    此时原字符串的对称中心由bb之间转换为#，
	原字符串回味串为==奇数== abad   -> #a#b#a#d#        此时对称中心还是b
STEP2：	根据两种情况对字符串进行遍历得到辅助数组$R$
STEP3： 遍历辅助字符串，根据辅助数组求得最长的字符串；

     

```c++
class Solution {
public:     //马拉车算法
    string getnewstring(string str) {
        string news = "#";
        for (int i = 0; i < str.size(); i++) {
            news += str[i];
            news += '#';
        }
        return news;
    }
    string longestPalindrome(string s) {
        string ns = getnewstring(s);
        int *r = new int[ns.size()], c;      
        r[0] = 1, c= 0;
        for (int i = 0; i < ns.size(); i++) {
            if (i >= c + r[c]) {
                r[i] = 1;
            } else {
                r[i] = min(r[2 * c - i], c + r[c] - i);
            }
            while (i - r[i] >= 0 && ns[i - r[i]] == ns[i + r[i]]) {
                r[i]++;
            }
            if (c + r[c] < i + r[i]) c = i;
        }
        int ans = 0; 
        string S = "";
        for (int i = 0; i < ns.size(); i++) {
            if (r[i]  <= ans) continue;
            ans = r[i];
            S = "";
            for (int j = i - r[i] + 1; j < i + r[i]; j++) {
                if (ns[j] == '#') continue;
                S += ns[j];
            }
        }
        return S;
    }
};
```