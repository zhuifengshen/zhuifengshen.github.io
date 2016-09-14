---
layout:     post
title:      "[LeetCode 383] Ransom Note"
subtitle:   "\"喜刷刷 \""
date:       2016-09-01 20:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - LeetCode 
    - 算法之道
---

#### 问题描述

Given  an  arbitrary  ransom  note  string  and  another  string  containing  letters from  all  the  magazines,  write  a  function  that  will  return  true  if  the  ransom   note  can  be  constructed  from  the  magazines ;  otherwise,  it  will  return  false.    

Each  letter  in  the  magazine  string  can  only  be  used  once  in  your  ransom  note. 

**Note:**
 You may assume that both strings contain only lowercase letters. 

```java
canConstruct("a", "b") -> false
canConstruct("aa", "ab") -> false
canConstruct("aa", "aab") -> true
```

### 解法一

**解题思路:**

根据题意, 意思是一个字符串中是否包含另一个字符串中的所有字母, 包括个数, 理解题意就很简单了, 直接使用一个数组来统计各个字符的数量, 然后进行比较即可.

**代码实现:**

```java
    private boolean canConstruct(String ransomNote, String magazine) {
        int[] chars = new int[26];
        for (int i = 0; i < magazine.length(); i++) {
            chars[magazine.charAt(i) - 'a']++;
        }
        for (int i = 0; i < ransomNote.length(); i++) {
            //如果该字母个数小于零, 则说明magazine中不能全包含该字母
            if (--chars[ransomNote.charAt(i) - 'a'] < 0) return false;
        }
        return true;
    }
```

### 解法二

**解题思路:**

思路跟解法一类似, 这里使用Java API HashMap实现.

**代码实现:**

```java
    private boolean canConstruct1(String ransomNote, String magazine){
        HashMap<Character,Integer> charsMap = new HashMap<>(magazine.length());
        for (int i = 0; i < magazine.length(); i++) {
            if (charsMap.containsKey(magazine.charAt(i))){
                charsMap.put(magazine.charAt(i), charsMap.get(magazine.charAt(i)) + 1);
            }else {
                charsMap.put(magazine.charAt(i), 1);
            }
        }
        for (int i = 0; i < ransomNote.length(); i++) {
            if (charsMap.containsKey(ransomNote.charAt(i))){
                //当字母个数为1时,则应该去除该节点
                if (charsMap.get(ransomNote.charAt(i)) == 1){
                    charsMap.remove(ransomNote.charAt(i));
                }else{
                    charsMap.put(ransomNote.charAt(i), charsMap.get(ransomNote.charAt(i)) - 1);
                }
            }else {
                //当字母不存在时,说明magazine中不能全包含该字母,直接返回false.
                return false;
            }
        }
        return true;
    }
```

### 总结

理解题意后, 这道题很简单, 通过这两种解法的主要是为了分别对比使用原生代码实现和使用Java API实现时在性能上的差异,其中解法一在LeetCode上的运行为20ms,而解法二使用了85ms,足足是解法一的四倍多时间,所以日常我们在实现方法时,特别是那些经常被调用的代码,我们可以尽量使用原生代码实现.