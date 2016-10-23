---
layout: post
title: "《Real World Haskell》 第五章读书笔记"
date: 2016-10-23 19:40
tags:
- haskell
---
# CHAPTER 5：编写 JSON 库
<!--more-->

## JSON 简介
JSON 支持四种基本类型值：字符串、数字、布尔值和一个特殊值， null。
JSON 还提供了两种复合类型：*数组*是值的有序序列，而*对象*则是“名字/值”对的无序收集器（unordered collection of name/value pairs）。其中对象的名字必须是字符串，而对象和数组的值则可以是任何 JSON 类型。

## 在 Haskell 中表示 JSON 数据
```Haskell
data JValue = JString String
            | JNumber Double
            | JBool Bool
            | JNull
            | JObject [(String, JValue)]
            | JArray [JValue]
              deriving (Eq, Ord, Show)

```

## Haskell 模块
```Haskell
-- file: ch05/SimpleJSON.hs
module SimpleJSON
    (
        JValue(..)
    ,   getString
    ,   getInt
    ,   getDouble
    ,   getBool
    ,   getObject
    ,   getArray
    ,   isNull
    ) where
```

## 编译 Haskell 代码
 SimpleJSON.hi是*接口文件*（interface file）
 SimpleJSON.o 则是*目标文件*（object file）

## 载入模块和生成可执行文件
Main.hs的名字和 main函数的命名是有特别含义的，要创建一个可执行文件， ghc 需要一个命名为 Main的模块，并且这个模块里面还要有一个 main函数，而 main函数在程序执行时会被调用。

## 打印 JSON 数据
JSON数据的序列化

## 类型推导是一把双刃剑
为函数编写类型签名，既可以移除我们实际想要的类型和编译器推导出的类型之间的分歧，也可以作为函数的一种文档，帮助阅读和理解函数的行为。
在刚开始的时候最好尽可能地为函数添加类型签名，然后随着对类型系统了解的加深，逐步放松要求。

## 更通用的转换方式
使用Prettify库做更好的打印



参考：
[《Real World Haskell》 Bryan O'Sullivan, Don Stewart, and John Goerzen](http://book.realworldhaskell.org/)
[《Real World Haskell》 中文翻译项目](http://cnhaskell.com/chp/2.html)