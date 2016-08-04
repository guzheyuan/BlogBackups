---
layout: post
title: "leetcode: 1.Two Sum"
date: 2016-08-04 10:21
tags:
- leetcode
---
## 题目
Given an array of integers, return indices of the two numbers such that they add up to a specific target.
You may assume that each input would have exactly one solution.
Example:
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].

## 解题思路
用hash表，O(N)的解法

## 代码
```javascript
/** 
 * @param {number[]} nums 
 * @param {number} target 
 * @return {number[]} 
 */  
var twoSum = function(nums, target) {  
    var map = {};  
    for(var i = 0; i < nums.length; i++) {  
        var need = target - nums[i];  
        if(typeof map[need] === 'number') {  
            return [map[need], i];  
        } else {  
            map[nums[i]] = i;  
        }  
    }  
};  
```