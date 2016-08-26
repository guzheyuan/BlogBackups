---
layout: post
title: Ninety Nine Haskell Problems（一）
date: 2016-08-26 21:45
tags:
- haskell
---
《Learn You a Haskell for Great Good!》过了一遍，感觉还是没有上手，于是去找了下看看有没有练手的题目可以实践实践，于是就有了这个。<!--more-->
题目源于这里https://wiki.haskell.org/H-99:_Ninety-Nine_Haskell_Problems
作为学习haskell的练习补充
# Problem 1
## 题目：
>难度：*
>Find the last element of a list.
(Note that the Lisp transcription of this problem is incorrect.)

输出一个列表（list）的最后一个元素

## 解题思路：
不需要思路了，直接上代码吧

## 代码：
```haskell
myLast :: [a] -> a
myLast [] = error "No end for empty lists!"
myLast [x] = x
myLast (_:xs) = myLast xs
```

## 运行结果
```haskell
*Main> myLast [3,4,5,6,7]
7
```

# Problem 2
## 题目：
>难度：*
>Find the last but one element of a list.
(Note that the Lisp transcription of this problem is incorrect.)

输出一个列表（list）倒数第二个元素

## 解题思路：
组合last和init

## 代码：
```haskell
myButLast :: [a] -> a
myButLast = last . init
```
## 运行结果
```haskell
Prelude> myButLast [1,2,3,4]
3
Prelude> myButLast ['a'..'z']
'y'
```

# Problem 3
## 题目：
>难度：*
>Find the K'th element of a list. The first element in the list is number 1.

寻找第k个元素

## 解题思路：
一个一个往后找

## 代码：
```haskell
elementAt :: [a] -> Int -> a
elementAt (x:_) 1 = x
elementAt (_:xs) k 
    | k < 1 = error "Index out of bounds" 
    | otherwise = elementAt xs (k - 1)
```

## 运行结果
```haskell
Prelude> elementAt [1,2,3] 2
2
Prelude> elementAt "haskell" 5
'e'
```