---
layout:     post
title:      "[LeetCode 1] Two Sum"
subtitle:   "\"喜刷刷\""
date:       2016-08-15 20:00:00
author:     "Devin"
header-img: "img/leetcode-algorithm.jpg"
tags:
    - 算法之道
    - LeetCode
---

> LeetCode, Let's Begin

### 问题描述

Given an array of integers, return indices of the two numbers such that they add up to a specific target.
You may assume that each input would have exactly one solution.

Example:

> Given nums = [2, 7, 11, 15], target = 9,
Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].

UPDATE (2016/2/13):
The return format had been changed to zero-based indices. Please read the above updated description carefully. 

### 解法一

**解题思路:**
暴力实现, 通过遍历所有两两组合判断, 算法复杂度:O(n^2)

**代码实现:**

```java
    public int[] twoSum(int[] nums, int target) {
            for(int i = 0; i < nums.length; i++){
                //此处j=i+1即可,因为之前部分已遍历过
                for (int j = i + 1; j < nums.length; j++) {
                    if (i != j && nums[i] == target - nums[j]){
                        return new int[]{i,j};
                    }
                }
            }
            throw new IllegalArgumentException("No two sum solution");
    }
```

### 解法二

**解题思路:**
通过新建节点类, 用来保存每个数的数值及其下标, 并实现Comparable接口以便对其进行排序,接着便可以利用有序数组的特性进行查找, 算法复杂度:O(n)

**代码实现:**

```java
    //定义Node节点类,用于保存数值和对应的下标索引
    static class Node implements Comparable<Node>{
        int value, index;
        public Node(int value, int index){
            this.value = value;
            this.index = index;
        }
        @Override
        public int compareTo(Node o) {
            return this.value - o.value;
        }
    }
    public int[] twoSum(int[] nums, int target){
        int[] indexs = new int[2];
        Node[] nodes = new Node[nums.length];
        for (int i = 0; i < nodes.length; i++) {
            nodes[i] = new Node(nums[i], i);
        }
        //对节点进行排序,以便下面比较两个数之和的大小
        Arrays.sort(nodes);
        int i = 0, j= nums.length - 1;
        while (i < j){
            if (nodes[i].value + nodes[j].value == target){
                break;
            }else if(nodes[i].value + nodes[j].value < target){
                i++;
            }else{
                j--;
            }
        }
        indexs[0] = nodes[i].index;
        indexs[1] = nodes[j].index;
        return indexs;
    }
```

### 解法三

**解题思路:**
使用HashMap存储每个数的数值及其下标, 然后使用HashMap的containKey方法便可实现算法复杂度为O(n)的遍历判断.

**代码实现:**

```java
    public int[] twoSum(int[] nums, int target){
        //将每个数及其下标作为HashMap的一个元素
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            map.put(nums[i], i);
        }
        //利用HashMap的containsKey方法, 对每个数进行判断
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            //此处注意两个数下标不能相同的判断
            if (map.containsKey(complement) && map.get(complement) != i){
                return new int[]{i, map.get(complement)};
            }
        }
        throw new IllegalArgumentException("No two sum solution");
    }
```

### 解法四

**解题思路:**
思路跟解法三相同, 不过对解法三进行进一步的优化, 即: 利用两个数在数组中位置有先后, 故在添加进HashMap的时候, 就可以进行是否存对应的值的判断了, 同时还可以省去两个数是否下标相同的判断, 算法复杂度O(n).

**代码实现:**

```java
    public int[] twoSum(int[] nums, int target){
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)){
                return new int[]{map.get(complement), i};
            }
            map.put(nums[i], i);
        }
        throw new IllegalArgumentException("No tow sum solution");
    }
```

### 总结
每个问题, 从不同的角度切入, 总会有不同的实现形式, 同时对于一个实现形式, 也可以不断优化每一步步骤, 从而让问题的解决方式更高效精简.