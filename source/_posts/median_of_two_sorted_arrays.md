---
layout: post
title: "leetcode: 4.Median of Two Sorted Arrays"
date: 2016-07-25 12:43
tags:
- leetcode
---
# Median of Two Sorted Arrays
### 题目
There are two sorted arrays nums1 and nums2 of size m and n respectively.
Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).
<!--more-->

Example 1:
nums1 = [1, 3]
nums2 = [2]
The median is 2.0

Example 2:
nums1 = [1, 2]
nums2 = [3, 4]
The median is (2 + 3)/2 = 2.5
### 解题思路
这题的主要思路是每次排除1/2k个
### 代码
``` javascript
/** 
 * @param {number[]} nums1 
 * @param {number[]} nums2 
 * @return {number} 
 */  
var findMedianSortedArrays = function(nums1, nums2) {  
   if((nums1.length + nums2.length) % 2 == 1) {  
       return findKthNumber(nums1,0,nums2,0,Math.ceil((nums1.length + nums2.length) / 2));  
   } else {  
       var mid1 = findKthNumber(nums1,0,nums2,0,(nums1.length + nums2.length) / 2);  
       var mid2 = findKthNumber(nums1,0,nums2,0,(nums1.length + nums2.length) / 2 + 1);  
       return (mid1 + mid2)/2;  
   }  
};  
  
var findKthNumber = function(nums1, begin1, nums2, begin2, k) {  
    var length1 = nums1.length - begin1;  
    var length2 = nums2.length - begin2;  
    // 保证nums1比nums2短  
    if(length1 > length2) {  
        return findKthNumber(nums2, begin2, nums1, begin1, k);  
    }  
    // 边界条件：第一个元素  
    if(k === 1) {  
        return nums1[begin1] < nums2[begin2] ? nums1[begin1] : nums2[begin2];  
    }  
    // 边界条件：一条数列已空  
    if(length1 === 0) {  
        return nums2[begin2 + k - 1];  
    }  
    // 如果两者都比Math.floor(k/2)长  
    var index = length1 < Math.floor(k/2) ? length1 : Math.floor(k/2);  
    // 每次排除k/2  
    if(nums1[begin1 + index - 1] < nums2[begin2 + index - 1]) {  
        return  findKthNumber(nums1, begin1 + index, nums2, begin2, k - index);  
    } else {  
        return  findKthNumber(nums1, begin1, nums2, begin2 + index, k - index);  
    }  
};  
```