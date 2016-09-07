---
layout:     post
title:      "[LeetCode 8] String to Integer(atoi)"
subtitle:   "\"喜刷刷\""
date:       2016-08-21 20:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - 算法之道
    - LeetCode
---

### 问题描述

Implement atoi to convert a string to an integer.

**Hint:** Carefully consider all possible input cases. If you want a challenge, please do not see below and ask yourself what are the possible input cases.

**Notes:** It is intended for this problem to be specified vaguely (ie, no given input specs). You are responsible to gather all the input requirements up front.

**Update (2015-02-10):**
The signature of the C++ function had been updated. If you still see your function signature accepts a const char * argument, please click the reload button to reset your code definition. 

**Requirements for atoi:**

The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.

If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.

If no valid conversion could be performed, a zero value is returned. If the correct value is out of the range of representable values, INT_MAX (2147483647) or INT_MIN (-2147483648) is returned.


### 解题思路

本题字符串转换为数值不能, 需要注意的是综合考虑各种可能存在的情况:

* 考虑输入字符可能的特殊情况, 为null, 或为一个空格, 或为多个空格
* 输入字符串中首字符可能为"-"或"+"字符
* 输入字符串中包含非法字符
* 输入字符串转换时可能上溢或下溢

### 解法一

**代码实现:**

```java
    private int myAtoi(String str) {
        //去除字符串前后空格
        String string = str.trim();
        //1.字符串为空时,返回0
        if (string.isEmpty()) return 0;
        //索引
        int i = 0;
        //正负标记
        boolean isNegative = false;
        //结果值
        int result = 0;
        //2.判断字符串第一个字符各种情况
        if (string.charAt(i) == '-'){
            isNegative = true;
            i++;
        }else if (string.charAt(i) == '+'){
            i++;
        }else if (Character.isDigit(string.charAt(i))){
            result = string.charAt(i) - '0';
            i++;
        }else{
            return 0;
        }
        //循环提取字符串的结果值(巧妙之处:判断非数字时,直接结束计算结果,满足字符串中后面存在计算字符的情况
        while (i < string.length() && Character.isDigit(string.charAt(i))){
            //3.处理溢出的情况(巧妙之处:这里直接将-2147483648作为溢出情况处理,因为反正负数溢出时返回值都是-2147483648,不影响结果的正确性
            if ((long)result * 10 > Integer.MAX_VALUE || Integer.MAX_VALUE - (string.charAt(i) - '0') < result * 10){
                return isNegative ? Integer.MIN_VALUE : Integer.MAX_VALUE;
            }
            //if (Integer.MAX_VALUE/10 < result || (Integer.MAX_VALUE/10 == result && Integer.MAX_VALUE%10 < (str.charAt(i) - '0'))){} //判断溢出
            result = result * 10 + string.charAt(i) - '0';
            i++;
        }
        return isNegative ? -result : result;
    }
```

### 解法二

仔细观察解法一, 发现存在不少可以优化的地方, 比如当传入str为null, 就会报错; 当在判断第一个字符各种情况时, 对于后面两种情况, 可以直接在while循环中处理, 避免代码冗余; 判断溢出时, 需要进行类型转换, 看起来比较繁杂, 也比较容易出错, 如果不小心将类型转换写成(long)(result * 10), 此时如果result * 10已经溢出了, 则达不到类型转换的目的, 因为类型转换需要在发生溢出之前分配好内存, 故改进如下:

**代码实现:**

```java
    private int myAtoi(String str) {
        //1.字符串为null或空时,返回0
        if (str == null || str.trim().isEmpty()) return 0;
        //去除字符串前后空格
        String string = str.trim();
        int i = 0;
        boolean isNegative = false;
        //result开始时直接定义为double类型, 方便做溢出判断
        double result = 0;
        //2.判断字符串第一个字符各种情况
        if (string.charAt(i) == '-'){
            isNegative = true;
            i++;
        }else if (string.charAt(i) == '+'){
            i++;
        }
        //循环提取字符串的结果值(巧妙之处:判断非数字时,直接结束计算结果,满足字符串中后面存在计算字符的情况
        while (i < string.length() && string.charAt(i) >= '0' && string.charAt(i) <= '9'){
            result = result * 10 + string.charAt(i) - '0';
            i++;
        }
        //3.处理溢出的情况(巧妙之处:这里直接将-2147483648作为溢出情况处理,因为反正负数溢出时返回值都是-2147483648,不影响结果的正确性
        if (result > Integer.MAX_VALUE){
            return isNegative ? Integer.MIN_VALUE : Integer.MAX_VALUE;
        }
        return isNegative ? (int)-result : (int)result;
    }
```

### 解法三

解法一和解法二中, 在判断溢出时, 都需要使用到更大数据访问的类型, 能不能在不转换类型的情况进行溢出判断呢?

**代码实现:**

```java
    private int myAtoi(String str) {
        //1.字符串为null或空时,返回0
        if (str == null || str.trim().isEmpty()) return 0;
        //去除字符串前后空格
        String string = str.trim();
        int i = 0;
        boolean isNegative = false;
        int result = 0;
        //2.判断字符串第一个字符各种情况
        if (string.charAt(i) == '-'){
            isNegative = true;
            i++;
        }else if (string.charAt(i) == '+'){
            i++;
        }
        //循环提取字符串的结果值(巧妙之处:判断非数字时,直接结束计算结果,满足字符串中后面存在计算字符的情况
        while (i < string.length() && string.charAt(i) >= '0' && string.charAt(i) <= '9'){
            //3.处理溢出的情况(巧妙之处:这里直接将-2147483648作为溢出情况处理,因为反正负数溢出时返回值都是-2147483648,不影响结果的正确性
            if (Integer.MAX_VALUE / 10 < result || (Integer.MAX_VALUE / 10 == result && Integer.MAX_VALUE % 10 < string.charAt(i) - '0')){
                return isNegative ? Integer.MIN_VALUE : Integer.MAX_VALUE;
            }
            result = result * 10 + string.charAt(i) - '0';
            i++;
        }
        return isNegative ? -result : result;
    }
```

### 总结

在看完题目敲代码前, 先综合考虑各种需要考虑的情况, 往往能避免在提交代码时各种报错而手足无措 , 同时, 敲完代码, 先在脑袋里建立逻辑模型预运行一下各种情况, 也是锻炼自己思维能力的不错方式, 这样即使在测试时出现问题, 也能不用调试便可发现问题之处, 加快纠错速度, 好处多多. 