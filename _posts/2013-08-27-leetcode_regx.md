---
layout: post
title: Regular Expression Matching(LeetCode)
tags: [LeetCode, Algorithm]
keywords: [LeetCode, Algorithm,算法]
---

{% highlight c++ %}
#include <iostream>
#include <string>

using namespace std;

bool isMatch(const char *s, const char *p)
{
	if(p[0] == '*')
		return false;
	else if(p[0] == 0)
		return s[0] == 0;
	else if(p[0] == '.')
	{
		if(p[1] == '*')
		{
			if(p[2] == 0)
				return true;

			if(isMatch(s, p+2))
				return true;

			for(const char *snext = s; ; ++snext)
			{
				if(snext[0] == 0)
					return false;
				if(isMatch(snext+1, p+2))
					return true;
			}
		}
		else
		{
			return s[0] && isMatch(s+1, p+1);
		}
	}
	else
	{
		// normal character
		if(p[1] == '*')
		{
			if(isMatch(s, p+2))
				return true;

			for(const char *snext=s; ; ++snext)
			{
				if(snext[0] != p[0])
					return false;

				if(isMatch(snext+1, p+2))
					return true;
			}
		}
		else
		{
			return (s[0] == p[0]) && isMatch(s+1, p+1);
		}
	}
}


int main()
{
	string s, p;
	while(true)
	{
		cin >> s >> p;
		cout << isMatch(s.c_str(),p.c_str()) << '\n';
	}
}
{% endhighlight %}
