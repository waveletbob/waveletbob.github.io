---
layout:  post
title: LeetCode题解
categories: 数据结构与算法
tags: LeetCode

---

## LeetCode ##

@刷第一遍时将思路记录下来，第二遍时直接按照思路实现

### 4th 中位数 ###


- 问题：O(log (m+n)内寻找两个数组（m+n）
- 思路：转化为求解第K大的数

![](http://www.programcreek.com/wp-content/uploads/2012/12/median-of-two-sorted-array-730x571.png)

### 最长回文子串 ###

四种解法：

- 暴力法，三个循环
- 动态规划
- 中心扩展
- Manacher

### ZigZag Conversion ###

- 解法：找规律，所有行的重复周期都是 2 * nRows - 2，对于首行和末行之间的行，还会额外重复一次，重复的这一次距离本周期起始字符的距离是 2 * nRows - 2 - 2 * i

### Reverse Integer ###

- 解法：利用除法的余数求出每一位的数字并加上，不过要考虑翻转后数字溢出边界。

### String To Integer ###

- 思路：按照去空-》判断符号-》构造每位数字-》判断溢出等步骤进行

###  Palindrome Number回文数 ###

- 思路：题目要求用O（1）的空间，因此无法对其进行转化的任何操作，通过在左右两边除以10和乘10的操作来进行判断。

### Regular Expression Matching正则匹配 ###

- 思路：这道题的通配符主要是* 和·，*表示前面的字符0个或多个相同的字符。因此

### 最大水桶容积 ###

- 思路：从左向右，从右向左两边计算，直到找到最大容积

### Integer to Roman ###

- 思路：首先要搞清楚罗马字符组成，然后利用%和/进行每一位字符的提取，利用字符数组取值。

### Integer to Roman ###

- 思路：运用switch结构对每种情况进行映射，然后计算每位的值叠加起来得到最终结果

### Longest Common Prefix ###

### 3Sum || 3Sum Closest ###






