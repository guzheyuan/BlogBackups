---
layout: post
title: HASKELL学习笔记（一）
date: 2016-07-30 11:01
tags:
- haskell
---
>很多东西你以为懂了，但当你在写下来的时候，你就觉得无从下手了。
>如果一件事情你不能讲清楚，十有八九你还没有完全理解。

本系列以概括加吐槽《haskell趣学指南》为主，希望在完成这一系列博文后真正走入函数式编程的大门。
<!--more-->
好了，让我们开始吧

## 那么，haskell是啥？
>haskell是一门纯函数式编程语言。在命令式语言中执行操作需要给电脑安排一组命令，随着命令的执行，状态就会随之发生改变。例如你给变量a赋值为5，而随后做了其它一些事情之后a就可能变成的其它值。有控制流程，你就可以重复执行操作。然而在函数式编程语言中，你不是像命令式语言那样命令电脑“要做什么”，而是通过用函数来描述出问题“是什么”，如“阶乘是指从1到某数间所有数字的乘积”。变量一旦赋值，就不可以更改了，你已经说了a就是5，就不能再另说a是别的什么数。做人不能食言，对不？所以说，函数式编程语言中的函数能做的唯一事情就是求值，因而没有副作用。一开始会觉得这很受限，不过好处也正源于此：若以同样的参数调用同一函数两次，得到的结果总是相同。这被称作“引用透明”。如此一来编译器就可以理解程序的行为，你也很容易就能验证一个函数的正确性，继而可以将一些简单的函数组合成更复杂的函数。

上面一段引自《hasekll趣学指南》，我来概括下：
haskell是啥？haskell是一门纯函数式编程语言。
纯函数式编程语言是啥？对应于面向对象编程“一切皆对象”，函数式编程就是“一切皆函数”。

## 惰性求值
函数在真正需要结果以前不会被求值。因此可以把程序仅看作是**数据的一系列变形**。

## 入门
这一章简单介绍了函数的调用，大致介绍了几种表达式的写法，并且告诉你
>从始至终我们一直都在使用函数。

函数不止有这样的（前缀函数）
```haskell 
ghci> min 9 10   
9   
ghci> min 3.4 3.2   
3.2   
ghci> max 100 101   
101  
```
还有这样的（中缀函数）
```haskell 
ghci> 5 == 5   
True   
ghci> 1 == 0   
False   
ghci> 5 /= 5   
False   
ghci> 5 /= 4   
True   
ghci> "hello" == "hello"   
True 
```
在haskell中，函数的调用不再由逗号分隔参数表，而是用空格来分隔。
```haskell 
 函数名 参数一 参数二 ...
```

## 启蒙：你的第一个函数
这一章介绍了函数的声明
在heskell中，函数的声明与它的调用形式大体相同，都是先函数名，后跟由空格分隔的参数表。在声明中要在 = 后面定义函数的行为。
```haskell 
doubleUs x y = x*2 + y*2  
```

## List入门
List是一种单类型的数据结构，可以用来存储多个类型相同的元素。我们可以在里面装一组数字或者一组字符，但不能把字符和数字装在一起。
:运算符可以连接一个元素到一个List或者字符串之中，而++运算符则是连接两个List。
```haskell 
ghci> 5:[1,2,3,4,5]  
[5,1,2,3,4,5] 
ghci> "hello" ++ " " ++ "world"   
"hello world"  
```
length返回一个List的长度。
```haskell 
ghci> length [5,4,3,2,1]   
5
```
null检查一个List是否为空。如果是，则返回True，否则返回False。应当避免使用xs==[]之类的语句来判断List是否为空，使用null会更好。
```haskell
ghci> null [1,2,3]   
False   
ghci> null []   
True 
```
## 德州区间
要得到包含1到20中所有自然数的List，只要[1..20]即可。
```haskell
ghci> [1..20]   
[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]   
ghci> ['a'..'z']   
"abcdefghijklmnopqrstuvwxyz"   
ghci> ['K'..'Z']   
"KLMNOPQRSTUVWXYZ"
```
haskell能从前两个元素自动推断步长，从而构造list
```haskell
ghci> [2,4..20]   
[2,4,6,8,10,12,14,16,18,20]   
ghci> [3,6..20]   
[3,6,9,12,15,18]  
```
尽量避免浮点数
```haskell
ghci> [0.1, 0.3 .. 1]   
[0.1,0.3,0.5,0.7,0.8999999999999999,1.0999999999999999] 
```

