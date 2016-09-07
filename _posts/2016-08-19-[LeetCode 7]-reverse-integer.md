---
layout:     post
title:      "[LeetCode 7] Reverse Integer"
subtitle:   "\"喜刷刷\""
date:       2016-08-19 20:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - 算法之道
    - LeetCode
---

### 问题描述

Reverse digits of an integer.

Example1: x = 123, return 321

Example2: x = -123, return -321 

**Have you thought about this?**

Here are some good questions to ask before coding. Bonus points for you if you have already thought through this!

If the integer's last digit is 0, what should the output be? ie, cases such as 10, 100.

Did you notice that the reversed integer might overflow? Assume the input is a 32-bit integer, then the reverse of 1000000003 overflows. How should you handle such cases?

For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

**Update (2014-11-10):**
Test cases had been added to test the overflow behavior. 

### 注意事项

* 处理负数情况;
* 处理尾数为零的情况;
* 处理反转之后数值溢出的情况, 判断方法: 利用Java API溢出时抛出异常; 执行前与最大值比较; 与上一次结果比较; 具体实现参考下面代码实现;

### 方法一

**解题思路:**

将数值转换为字符串, 再将字符串转换为字符数组, 接着倒置数组, 然后反过来换为数值即可.

**代码实现:**

```java
    public int reverse(int x){
        boolean isNegative = false;
        int result;
        if (x < 0){
            //正负标记
            isNegative = true;
            x = -x;
        }
        //数值转换为字符串
        String number = String.valueOf(x);
        //字符串转换为字符数组
        char[] numbers = number.toCharArray();
        int i = 0, j = numbers.length -1;
        //倒置字符数组
        while (i < j){
            char temp = numbers[i];
            numbers[i] = numbers[j];
            numbers[j] = temp;
            i++;
            j--;
        }
        //现在反过来,将字符数组转换为字符串,将字符串转换为数组,如果溢出会抛出的异常,利用这个来判断是否溢出
        try{
            result = isNegative ? -Integer.valueOf(new String(numbers)) : Integer.valueOf(new String(numbers));
        }catch (NumberFormatException e){
            return 0;
        }
        return result;
    }
```

### 方法二

**解题思路:**

利用求余, 不断获得最后一位, 进行倒置.

**代码实现:**

```java
    public int reverse(int x){
        boolean flag = false;
        int result = 0;
        //转换为正数
        if (x < 0){
            x = -x;
            flag = true;
        }
        //循环倒置
        while (x > 0) {
            //判断是否溢出
            if (((long)result * 10) > Integer.MAX_VALUE || Integer.MAX_VALUE - x % 10 < result * 10) return 0;
            result = result * 10 + x % 10;
            x /= 10;
        }
        return flag ? -result : result;
    }
```

### 方法三

**解题思路:**

在方法三基础上, 想起其实负数也可以直接求余, 于是进一步简化.

**代码实现:**

```java
    public int reverse(int x){
        int result = 0;
        //循环倒置
        while (x != 0) {
            //判断是否溢出
            if (Math.abs((long)result * 10) > Integer.MAX_VALUE || Integer.MAX_VALUE - Math.abs(x % 10) < Math.abs(result * 10)) return 0;
            result = result * 10 + x % 10;
            x /= 10;
        }
        return result;
    }
```

### 方法四

**解题思路:**

在方法三和方法四中, 觉得判断溢出需要进行类型转换太麻烦, 继续优化

**代码实现:**

```java
    public int reverse(int x){
        int result = 0;
        //循环倒置
        while (x != 0){
            //判断是否溢出
            if (Integer.MAX_VALUE / 10 < result || (Integer.MAX_VALUE / 10 == result && Integer.MAX_VALUE % 10 < x % 10)) return 0;
            result = result * 10 + x % 10;
            x /= 10;
        }
        return result;
    }
```

### 方法五

**解题思路:**

优化溢出判断之后, 感觉还是很冗长, 我们继续优化.

**代码实现:**

```java
    public int reverse(int x){
        int result = 0;
        while (x != 0){
            //使用临时变量, 用于标记是否发生异常
            int temp = result * 10 + x % 10;
            x /= 10;
            //判断是否溢出
            if (temp / 10 != result) return 0;
            result = temp;
        }
        return result;
    }
```

### 总结

不断切换思考的角度, 总是可以发现新的思路来更好地解决当前的问题, 这样正是编程之美, 享受思维火花绽放的快意和极致.