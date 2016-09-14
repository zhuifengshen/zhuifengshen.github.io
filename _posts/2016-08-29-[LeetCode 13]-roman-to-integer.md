---
layout:     post
title:      "[LeetCode 13] Roman To Integer"
subtitle:   "\"喜刷刷 \""
date:       2016-08-29 20:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - LeetCode 
    - 算法之道
---

#### 问题描述

Given a roman numeral, convert it to an integer.

Input is guaranteed to be within the range from 1 to 3999.

**知识准备:**

题意很简单, 就是将罗马数字转换为整型, 那么何为罗马数字呢? 请看: [百科:罗马数字](http://baike.baidu.com/link?url=KPtlECZIsaJDNzjQ5kKFaVyCY4vpIU5qxAuXrUncrWd_ctDi1q4H3HFMEkVpAU8Q)

从百科上我们可知罗马数字的特点:

* 相同的数字连写、所表示的数等于这些数字相加得到的数, 如：Ⅲ=3；

* 小的数字在大的数字的右边、所表示的数等于这些数字相加得到的数, 如：Ⅷ=8、Ⅻ=12；

* 小的数字、（限于 Ⅰ、X 和 C）在大的数字的左边、所表示的数等于大数减小数得到的数、且放在大数左边的只能一个, 如：Ⅳ=4、Ⅸ=9；

* 正常使用时、连写的数字重复不得超过三次；

* 在一个数的上面画一条横线、表示这个数扩大 1000 倍。

### 解法一

**解题思路:**

使用一个HashMap来存储罗马数字的字符及其对应的权值, 然后依次一个循环读取即可.

**代码实现:**

```java
    public int romanToInt(String s){
        Map<Character,Integer> romanMap = new HashMap<>(7);
        romanMap.put('I',1);
        romanMap.put('V',5);
        romanMap.put('X',10);
        romanMap.put('L',50);
        romanMap.put('C',100);
        romanMap.put('D',500);
        romanMap.put('M',1000);
        int length = s.length();
        int result = 0;
        if (s.length()==1) return romanMap.get(s.charAt(0));
        for (int i = 1; i < s.length(); i++) {
            if (romanMap.get(s.charAt(i)) <= romanMap.get(s.charAt(i-1))){
                result += romanMap.get(s.charAt(i-1));
            }else {
                result -= romanMap.get(s.charAt(i-1));
            }
        }
        result += romanMap.get(s.charAt(s.length()-1));
        return result;
    }
```

### 解法二

**解题思路:**

通过解法一我们发现最后一个罗马数字一定会被添加, 这样如果倒序读取罗马数字,就可以减少一些没必要的判断, 那么优化一下.

**代码实现:**

```java
    public static int romanToInt(String s) {
        Map<Character,Integer> romanMap = new HashMap<>(7);
        romanMap.put('I',1);
        romanMap.put('V',5);
        romanMap.put('X',10);
        romanMap.put('L',50);
        romanMap.put('C',100);
        romanMap.put('D',500);
        romanMap.put('M',1000);
        int length = s.length();
        int result = romanMap.get(s.charAt(length-1));
        for (int i = length-2; i >= 0; i--) {
            if (romanMap.get(s.charAt(i+1)) <= romanMap.get(s.charAt(i))){
                result += romanMap.get(s.charAt(i));
            }else {
                result -= romanMap.get(s.charAt(i));
            }
        }
        return result;
```

### 解法三

**解题思路:**

继续优化, 用原生代码实现, 因为罗马数字都为字母, 字母只要26个, 所以考虑使用一个包含26个元素的数组存储其对应的数值来实现.

**代码实现:**

```java
    public static int romanToInt(String s){
        int[] roman = new int[26];
        roman['I'-'A'] = 1;
        roman['V'-'A'] = 5;
        roman['X'-'A'] = 10;
        roman['L'-'A'] = 50;
        roman['C'-'A'] = 100;
        roman['D'-'A'] = 500;
        roman['M'-'A'] = 1000;
        int length = s.length();
        int result = roman[s.charAt(length-1)-'A'];
        for (int i = length - 2; i >= 0; i--) {
            if (roman[s.charAt(i+1)-'A'] <= roman[s.charAt(i)-'A']){
                result += roman[s.charAt(i) - 'A'];
            }else {
                result -= roman[s.charAt(i) - 'A'];
            }
        }
        return result;
```

### 总结

第一种解法耗时18ms, 第二种解法耗时26ms, 第三种解法耗时8ms, 第二种解法虽然优化了冗余的判断, 但提交系统上的测试用例只需要提前判断即可马上通过, 因而性能反而是第一种快, 看到第三种解法的耗时时, 我们可以明显感觉到原生代码实现可以极大提高代码的性能, 同时利用字母与数组的对应关系也是一个不错的技巧.