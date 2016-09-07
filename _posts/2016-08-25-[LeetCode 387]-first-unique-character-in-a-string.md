---
layout:     post
title:      "[LeetCode 387] First Unique Character in a String"
subtitle:   "\"喜刷刷\""
date:       2016-08-25 20:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - 算法之道
    - LeetCode
---

### 问题描述

 Given a string, find the first non-repeating character in it and return it's index. If it doesn't exist, return -1.

**Examples:**

```java
s = "leetcode"
return 0.

s = "loveleetcode",
return 2.
```

**Note:**You may assume the string contain only lowercase letters. 

### 方法一

**解题思路:**

直接逐个暴力查找, 算法复杂度O(n^2)

**代码实现:**

```java
    public static int firstUniqChar(String s){
        for (int i = 0; i < s.length(); i++) {
            boolean isUnique = true;
            for (int j = 0; j < s.length(); j++) {
                if (i != j && s.charAt(i) == s.charAt(j)){
                    isUnique = false;
                    break;
                }
            }
            if (isUnique) return i;
        }
        return -1;
    }
```

### 方法二

**解题思路:**

使用HashMap来存储字符串中每个字符出现的次数, 然后一次遍历即可实现, 算法复杂度:O(n)

**代码实现:**

```java
    public int firstUniqChar(String s){
        //首先,使用HashMap存储每个字符出现的个数
        Map<Character, Integer> charMap = new HashMap<>(s.length()); //预先分配HashMap大小,避免扩容性能影响
        for (int i = 0; i < s.length(); i++) {
            if (!charMap.containsKey(s.charAt(i))){
                charMap.put(s.charAt(i), 1);
            }else {
                charMap.put(s.charAt(i), charMap.get(s.charAt(i))+1);
            }
        }
        //遍历字符串字符,获取第一个出现次数为1的字符下标
        for (int i = 0; i < s.length(); i++) {
            if (charMap.get(s.charAt(i)) == 1){
                return i;
            }
        }
        return -1;

        //注意: 获取entrySet时,顺序已经被打乱,因此不能使用
        //for (Map.Entry<Character, Integer> entry : charMap.entrySet()){
        //    System.out.println(entry.getKey() + " : " + entry.getValue());
        //    if (entry.getValue() == 1){
        //        return s.indexOf(entry.getKey());
        //    }
        //}
    }
```

### 方法三

方法一比较简单粗暴, 方法二比较冗余, 继续优化: 使用一个数组计数, 巧妙实现

**代码实现:**

```java
    public int firstUniqChar(String s){
        int[] countArray = new int[26];
        //首先使用数组countArray记录每个字母出现的次数
        for (int i = 0; i < s.length(); i++) {
            countArray[s.charAt(i) - 'a']++;
        }
        //然后取出最先出现次数为1的字母的下标
        for (int i = 0; i < s.length(); i++) {
            if (countArray[s.charAt(i) - 'a'] == 1) return i;
        }
        return -1;
    }
```

### 总结

解决一个浅显问题时, 用简单直接的方式解决, 虽然有时候可以完成, 但效率往往比较低下;这时如果耐心下来细致地观察分析问题, 或许便可观察到其中暗藏的性质或规律, 就像本题中, 可联想到26个字母, 使用一个包含26个元素的数组便可完成个数统计, 简单快捷.