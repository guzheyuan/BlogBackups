---
layout: post
title: "《Real World Haskell》 第二章读书笔记"
date: 2016-09-13 13:45
tags:
- haskell
---
# CHAPTER 2： 类型与函数
<!--more-->
## 为什么要关注类型?
了解Haskell的类型系统可以更好的根据其特性，写出精确的、健壮的代码。

## Haskell的类型系统
Haskell类型系统的三大特征
1.强类型
每个表达式和函数都有确定的类型。Hasekll的编译器或者解释器在我们使用一个表达式时会检测他的类型是否匹配，一旦发现不匹配则会拒绝执行这段代码并返回一个错误消息。（而不是做自动类型转换）
2.静态类型
编译时确定每一个变量的类型而不是在运行时。
3.自动推导
Haskell有强大的类型推导系统。

## 类型系统给了我们什么
静态、强类型检查使得 Haskell 更安全，而类型推导则让它更精炼、简洁。这样得出的的结果是，比起其他流行的静态语言，Haskell 要来得更安全，而比起其他流行的动态语言， Haskell 的表现力又更胜一筹。

## 一些常用的基本类型
Char
Bool
Int
Intger
Double
## 一些常用的函数
odd
compare

## 列表与元组
List：[1,2,3,4]
Tuple：("abc",1)

## 列表与元组相关函数
List：take、drop
Tuple：fst、snd

## 函数类型与纯度
副作用：副作用本质上是函数的一种不可见的（invisible）输入或输出。
我们将带副作用的函数称为“不纯（impure）函数”，而将不带副作用的函数称为“纯（pure）函数”。

## 理解求值
变量不可赋值
惰性求值

## 多态
参数多态：Haskell 的参数多态直接影响了 Java 和 C# 等语言的泛型（generic）功能的设计。Java 泛型中的类型变量和 Haskell 的参数化类型非常相似。而 C++ 的模板也和参数多态相去不远。
子类多态：继承，Haskell不是面向对象，不支持
强制多态：隐式转换，Haskell坚决反对

## 为什么要对纯度斤斤计较？
软件的大部分风险，都来自于与外部世界进行交互：它需要程序去应付错误的、不完整的数据，并且处理恶意的攻击，诸如此类。Haskell 的类型系统明确地告诉我们，哪一部分的代码带有副作用，让我们可以对这部分代码添加适当的保护措施。

## 总结
这一章对 Haskell 的类型系统以及类型语法进行了快速的概览，了解了基本类型，并学习了如何去编写简单的函数。这章还介绍了多态、条件表达式、纯度和惰性求值。

参考：
[《Real World Haskell》 Bryan O'Sullivan, Don Stewart, and John Goerzen](http://book.realworldhaskell.org/)
[《Real World Haskell》 中文翻译项目](http://cnhaskell.com/chp/2.html)