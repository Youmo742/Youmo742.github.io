---
title: KMP算法
categories: 数据结构
date: 2018-02-08 17:12:58
tags:
- 模式串匹配
- 串
---

# KMP

在上面的串的操作的时候，求子串的位置，我们用的是暴力求解法，即一个字符一个字符的进行匹配，每当一个字符不匹配的时候，模式串的字符向后滑动一位，重新匹配，这样的方法也叫回溯法。当字符串字符很多时，回溯花费的时间很大，而KMP算法，就是为了减少那些不必要的回溯。

# 匹配方法

KMP算法的原理就是把那些不必要回溯的的字符个数存储在一个next数组中，每当不匹配的时候，主串不用回溯，将模式串向后滑动next数组值的位数，重新匹配。下面先直接给出 KMP 的算法流程

- 假设现在文本串 S 匹配到 i 位置，模式串 P 匹配到 j 位置
  - 如果 j = -1，或者当前字符匹配成功（即 S[i] == P[j]），都令 i++，j++，继续匹配下一个字符；
  - 如果 j != -1，且当前字符匹配失败（即 S[i] != P[j]），则令 i 不变，j = next[j]。此举意味着失配时，模式串 P 相对于文本串 S 向右移动了 j - next [j] 位。
    - 换言之，当匹配失败时，模式串向右移动的位数为：失配字符所在位置 - 失配字符对应的 next 值，即**移动的实际位数为：j - next[j]**，且此值大于等于1。

- 对于这个算法，看了好几遍感觉还是迷。其实就是，举个例子如果主串为`"BBC ABCDAB ABDBC DGH"`而模式串为`"ABCDABD"`.这样的话，当主串在第11个字符和模式串第七个字符失配了，如果用前面的回溯法，就是让模式串第1个字符继续和主串第5个字符继续匹配，而KMP算法就是直接让模式串向后滑动4个字符，继续匹配。

  ![](http://wiki.jikexueyuan.com/project/kmp-algorithm/images/3341.png)

  ![](http://wiki.jikexueyuan.com/project/kmp-algorithm/images/3342.png)

  图片是抄的，但是很适合这个场景。当字符D失配时，在模式串D前面`"ABCDAB"`中，最前面的AB和后面的AB是一样的，而后一个AB和主串已经匹配，那么，直接就让前面的AB滑动到与主串AB对应的位置。而这个滑动的距离，就是`j-next[j]`,所以，next数组中所存储的就是，各个字符前字符串的子字符前缀后缀字符串最大相同字符串的字符个数，并且让`next[0]=-1;`。博大的KMP算法不多说了，详见此博客[KMP算法详解](http://wiki.jikexueyuan.com/project/kmp-algorithm/define.html) 。


# 求next数组方法

next数组值，只与模式串有关，代码如下

```C++
void GetNext(char* p, int next[])
{
	int pLen = strlen(p);
	next[0] = -1;
	int k = -1;
	int j = 0;
	while (j < pLen - 1)
	{
		//p[k]表示前缀，p[j]表示后缀  
		if (k == -1 || p[j] == p[k])
		{
			++k;
			++j;
			next[j] = k;
		}
		else
		{
			k = next[k];
		}
	}
}
```

# 完整的代码

```C++
#include <iostream>
#include <cstdio>

using namespace std;
void GetNext(char* p, int next[])
{
	int pLen = strlen(p);
	next[0] = -1;
	int k = -1;
	int j = 0;
	while (j < pLen - 1)
	{
		//p[k]表示前缀，p[j]表示后缀  
		if (k == -1 || p[j] == p[k])
		{
			++k;
			++j;
			next[j] = k;
		}
		else
		{
			k = next[k];
		}
	}
}
int KmpSearch(char* s, char* p)
{
	int i = 0;
	int j = 0;
	int sLen = strlen(s);
	int pLen = strlen(p);
	int next[255];
	GetNext(p, next);
	while (i < sLen && j < pLen)
	{  
		if (j == -1 || s[i] == p[j])
		{
			i++;
			j++;
		}
		else
		{       
			j = next[j];
		}
	}
	if (j == pLen)
		return i - j;
	else
		return -1;
}
int main()
{
	char *s = "ababaacaba";
	int next[255];
	//GetNext(s, next);
	char *p = "cab";
	cout << KmpSearch(s, p);
	system("pause");
	return 0;
}
```

