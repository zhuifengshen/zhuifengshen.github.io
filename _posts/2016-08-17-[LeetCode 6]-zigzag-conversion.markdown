---
layout:     post
title:      "[LeetCode 6] ZigZag Conversion"
subtitle:   "\"喜刷刷\""
date:       2016-08-17 20:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - 算法之道
    - LeetCode
---

> One man’s crappy software is another man’s full time job  -- Jessica Gaston 

### 问题描述

The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility) 

	P   A   H   N
	A P L S I I G
	Y   I   R

And then read line by line: "PAHNAPLSIIGYIR"

Write the code that will take a string and make this conversion given a number of rows:

`string convert(string text, int nRows);`

convert("PAYPALISHIRING", 3) should return "PAHNAPLSIIGYIR". 

### 解题思路

根据题意, 字符串以曲折线排列, 需要我们以横向一行一行读取, 为了方便理解, 我们可以使用数字排列, 然后分行数列举并寻找规律:

numRows=2时，字符串坐标变成zigzag的走法就是：

	 0 2 4 6

	 1 3 5 7

numRows=3时的走法是：

	 0     4     8

	 1  3  5  7  9

	 2     6    10

numRows=4时的走法是：

	 0       6        12

	 1    5  7    11  13

	 2  4    8  10    14

	 3       9        15 

通过观察发现, 首行和尾行相邻两个元素之间的距离是2 * (numRows - 1), 比如n=4, 第一行0到6距离为2 * (4-1) = 6; 而其他行除了像首末两行一样有间隔距离2 * (numRows - 1)的元素外，在它们之间还有一个元素，该元素到该行下一个元素的距离为2*i，i表示所在行数的index, 即numRows-1，比如n=4时, 第二行5到7距离为2 * (2-1)=2; 因此可知, 非 固定间隔位置到下一个元素的距离为2 * (numRows -1) - 2 * i.

### 代码实现

```java
    private String convert(String s, int numRows) {
        //字符串为空则直接返回
        if (s == null || s.isEmpty()){
            return s;
        }
        int length = s.length();
        //字符串长度不大于行数或者行数为1时，直接返回
        if (length <= numRows || numRows == 1){
            return s;
        }
        StringBuilder sb = new StringBuilder();
        //首行和尾行相邻两个元素之间的距离
        int step = 2 * numRows - 2;
        //统计字符已添加个数
        int count = 0;
        //逐行添加，行数从零开始计算
        for (int i = 0; i < numRows; i++) {
            //其他行除了像首末两行一样有间隔距离2*(numRows - 1)的元素，在它们之间还有一个元素，该元素到该行下一个元素的距离为2*i，i为所在行数，所以可知固定间隔位置到下一个元素的距离为2*(numRows -1) - 2*i，即step - interval;
            int interval = step - 2 * i;
            for (int j = i; j < s.length(); j += step) {
                sb.append(s.charAt(j));
                count++;
                //interval > 0 排除末行，interval < step 排除首行
                if (interval > 0 && interval < step && j + interval < length && count < length){
                    sb.append(s.charAt(j + interval));
                    count++;
                }
            }
        }
        return sb.toString();
    }
```

### 总结

这道题比较注重规律的寻找, 通过使用简单数字的转化, 从而快速总结出规律, 剩下的就是简单运算了.