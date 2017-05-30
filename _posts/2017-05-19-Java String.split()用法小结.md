---
layout:     post                            # 使用的布局（不需要改）
title:      Java String.split()用法小结                   # 标题
subtitle:   String.split(".") → String.split("\\.")        #副标题
date:       2017-05-19                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Java
---

# Java String.split()用法小结

在java.lang包中有String.split()方法,返回是一个数组
我在应用中用到一些,给大家总结一下,仅供大家参考:
1、如果用“.”作为分隔的话,必须是如下写法,String.split("\\."),这样才能正确的分隔开,不能用String.split(".");
2、如果用“|”作为分隔的话,必须是如下写法,String.split("\\|"),这样才能正确的分隔开,不能用String.split("|");
“.”和“|”都是转义字符,必须得加"\\";
3、如果在一个字符串中有多个分隔符,可以用“|”作为连字符,比如,“acount=? and uu =? or n=?”,把三个都分隔出来,可以用String.split("and|or");
使用String.split方法分隔字符串时,分隔符如果用到一些特殊字符,可能会得不到我们预期的结果。 
我们看jdk doc中说明  
public String[] split(String regex)
 Splits this string around matches of the given regular expression. 
参数regex是一个 regular-expression的匹配模式而不是一个简单的String,他对一些特殊的字符可能会出现你预想不到的结果,比如测试下面的代码用竖线 | 分隔字符串,你将得不到预期的结果
```
	String[] aa = "aaa|bbb|ccc".split("|"); 
	//String[] aa = "aaa|bbb|ccc".split("\\|"); 这样才能得到正确的结果 
	for (int i = 0 ; i <aa.length ; i++ ) { 
		System.out.println("--"+aa[i]); 
	}
```

用竖 * 分隔字符串运行将抛出java.util.regex.PatternSyntaxException异常,用加号 + 也是如此。
```
	String[] aa = "aaa*bbb*ccc".split("*"); 
	//String[] aa = "aaa|bbb|ccc".split("\\*"); 这样才能得到正确的结果 
	for (int i = 0 ; i <aa.length ; i++ ) { 
		System.out.println("--"+aa[i]); 
	}
```

显然, + * 不是有效的模式匹配规则表达式,用"\\*" "\\+"转义后即可得到正确的结果。
"|" 分隔串时虽然能够执行,但是却不是预期的目的,"\\|"转义后即可得到正确的结果。
还有如果想在串中使用"\"字符,则也需要转义.首先要表达"aaaa\bbbb"这个串就应该用"aaaa\\bbbb",如果要分隔就应该这样才能得到正确结果,
```
	String[] aa = "aaa\\bbb\\bccc".split("\\\\");
```