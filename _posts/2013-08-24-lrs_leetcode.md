---
layout: post
title: Longest Palindromic Substring(LeetCode)
tags: [LeetCode, Algorithm]
keywords: [LeetCode, Algorithm, Palindromic, 回文子串]
---

{% highlight c++ linenos %}
#include <string>
#include <iostream>
using namespace std;

string longestPalindrome(string s)
{
    // Start typing your C/C++ solution below
    // DO NOT write int main() function

	int size = s.size();
	char *str = new char[size*2+3];
	str[0] = '$';
	str[1] = '#';
	str[size*2+2] = 0;
	for(int i = 0; i < size; ++i)
	{
		str[2*i+2] = s[i];
		str[2*i+3] = '#';
	}

	int *p = new int[size*2+2];
	memset(p, 0, sizeof(int)*(size*2+2));
	p[0] = 1;
	int mid = 0;
	int mx = 1;
	int maxi = 0;
	int maxlen = 1;
	for(int i = 1; str[i]; ++i)
	{
		if(mx > i)
			p[i] = min(p[2*mid-i], mx-i);
		else
			p[i] = 1;

		while(str[i-p[i]] == str[i+p[i]])
			p[i]++;

		if(mx < i+p[i])
		{
			mx = i + p[i];
			mid = i;
		}

		if(maxlen < p[i])
		{
			maxlen = p[i];
			maxi = i;
		}
	}

	delete str;
	delete p;

	return s.substr((maxi-maxlen+1)/2, maxlen-1);
}

int main()
{
	string s;
	while(true)
	{
		cin >> s;
		cout << longestPalindrome(s) << '\n';
	}
}
{% endhighlight %}
