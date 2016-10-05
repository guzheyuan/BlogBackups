---
layout: post
title: "《Real World Haskell》 第四章读书笔记"
date: 2016-09-23 16:33
tags:
- haskell
---
# CHAPTER 4：函数式编程
<!--more-->

## 使用 Haskell 思考
从命令式语言转换到函数式语言上面来

## 一个简单的命令行程序

```
-- file: ch04/InteractWith.hs

import System.Environment (getArgs)

interactWith function inputFile outputFile = do
  input <- readFile inputFile
  writeFile outputFile (function input)

main = mainWith myFunction
  where mainWith function = do
          args <- getArgs
          case args of
            [input,output] -> interactWith function input output
            _ -> putStrLn "error: exactly two arguments needed"

        -- replace "id" with the name of our function below
        myFunction = id
```
这是一个简单但完整的文件处理程序
当我们需要测试其他函数的时候，我们就将程序中的 id 换成其他函数的名字。

## 循环的表示
fold、map以及filter
foldl带来的栈溢出问题

## 匿名（lambda）函数
匿名函数各个方面的行为都和带名称的函数基本一致，但是，匿名函数的定义受到几个严格的限制，其中最重要的一点是：普通函数可以通过多条语句来定义，而 lambda 函数的定义只能有一条语句。

## 部分函数应用和柯里化
在 Haskell 中，所有函数都只接受一个参数。

## As-模式
 @符号，模式 xs@(_:xs')被称为 as-模式，它的意思是：如果输入值能匹配 @符号右边的模式，那么就将这个值绑定到 @符号左边的变量中。

## 通过组合函数来进行代码复用
(.)操作符
```
*Main Data.List> :type (.)
(.) :: (b -> c) -> (a -> b) -> a -> c
```

## 编写可读代码的提示
尽量使用map 、 take 和 filter而不是折叠、显式递归解决问题。

## 内存泄漏和严格求值
foldl':左折叠的严格版本，它使用特殊的 seq函数来绕过 Haskell 默认的非严格求值
seq:强迫（force）求值传入的第一个参数，然后返回它的第二个参数。

参考：
[《Real World Haskell》 Bryan O'Sullivan, Don Stewart, and John Goerzen](http://book.realworldhaskell.org/)
[《Real World Haskell》 中文翻译项目](http://cnhaskell.com/chp/2.html)