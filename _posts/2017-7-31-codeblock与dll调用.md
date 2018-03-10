---
layout: post
title: CodeBlock调用第三方dll
categories: C/C++
tags: C++

--- 

### 1)环境 ###

- CodeBlock(分为带GCC和不带Gcc)
- 创建工程
- 创建Sources/Headers等目录文件
- dll初始化

### 2）DLL调用 ###

- 函数声明
	
typedef int(*DLLfun)(char para1,int para2...); 

- dll加载

HINSTANCE hdll=LoadLibrary（PATH);
- 函数获取

Dllfun init=(Dllfun)GetProcAddress(hdll,"funcname");

- 函数使用极其参数传递

init(para);



    