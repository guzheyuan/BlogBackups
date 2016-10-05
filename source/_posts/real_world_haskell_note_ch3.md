---
layout: post
title: "《Real World Haskell》 第三章读书笔记"
date: 2016-09-17 22:12
tags:
- haskell
---
# CHAPTER 3：定义类型，简化函数
<!--more-->
## 定义一个新的数据类型
data关键字用于定义新的数据类型
```
data BookInfo = Book Int String [String] 
     deriving (Show)
```
值构造器&类型构造器
值构造器和类型构造器可以同名

## 类型的别名
type关键字用于设置类型别名
```Haskell
type CustomerID = Int
type ReviewBody = String
data BetterReview = BetterReview BookInfo CustomerID ReviewBody
```
## 代数数据类型
C 结构和 Haskell 的代数数据类型最大的差别是，代数数据类型的成分是匿名且按位置排序的

## 模式匹配
本质上就是匹配值构造器，并将传给构造器的值绑定到变量上。
因为模式匹配的过程就像是逆转一个值的构造（construction）过程，因此它有时候也被称为解构（deconstruction）。
模式匹配的“深度”并没有限制。
在给一个类型写一组匹配模式时，一定要涵盖构造器的所有可能情况。

## 记录语法（Record Syntax）
定义一种数据类型的同时，定义好每个成分的访问器。
访问器的本质是一个函数。

## 参数化类型（Parameterized Types）
即范型，是类型的多态

## 递归类型
比如List:
```
data List a = Cons a (List a) 
            | Nil 
              deriving (Show)
```
即它用自己来定义自己。

参考：
[《Real World Haskell》 Bryan O'Sullivan, Don Stewart, and John Goerzen](http://book.realworldhaskell.org/)
[《Real World Haskell》 中文翻译项目](http://cnhaskell.com/chp/2.html)