---
layout:     post
title:      "[LeetCode 9] Palindrome Number"
subtitle:   "\"喜刷刷\""
date:       2016-08-23 20:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - 算法之道
    - LeetCode
---

Determine whether an integer is a palindrome. Do this without extra space.

**Some hints:**

Could negative integers be palindromes? (ie, -1)

If you are thinking of converting the integer to string, note the restriction of using extra space.

You could also try reversing an integer. However, if you have solved the problem "Reverse Integer", you know that the reversed integer might overflow. How would you handle such case?

There is a more generic way of solving this problem.

### 方法一

**解题思路:**

转换为字符, 然后依次进行前后对比即可

**代码实现:**

```java
    private boolean isPalindrome(int x) {
        //负数为非回文串
        if (x < 0){
            return false;
        }
        //数值转换为字符串
        String string = String.valueOf(x);
        for (int i = 0; i <= string.length()/2; i++) {
            if (string.charAt(i) != string.charAt(string.length()-1-i)) return false;
        }
        return true;
    }
```

### 方法二

**解题思路:**

将数值倒置, 判断倒置后是否还相等

**代码实现:**

```java
    private boolean isPalindrome(int x){
        //负数为非回文串
        if (x < 0){
            return false;
        }
        int temp = 0, x1 = x;
        while (x != 0){
            //回文串倒置后相等,如果倒置出现溢出,说明为非回文串
            if (Integer.MAX_VALUE / 10 < temp || (Integer.MAX_VALUE / 10 == temp && Integer.MAX_VALUE % 10 < x % 10)){
                return false;
            }
            temp = temp * 10 + x % 10;
            x /=10;
        }
        if (temp == x1){
            return true;
        }else {
            return false;
        }
    }
```

### 总结

无论多简单的问题, 都要在写代码前思考好需要考虑的点, 充分考虑各种可能存在的情况, 比如本题中要考虑正负数和是否溢出等情况.