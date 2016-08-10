---
layout: post
title: HASKELL学习笔记（三）
date: 2016-08-10 14:48
tags:
- haskell
---
## 模式匹配
>模式匹配通过检查数据的特定结构来检查其是否匹配，并按模式从中取得数据。

这句话怎么理解呢，我们先看一段代码
```haskell
lucky :: (Integral a) => a -> String   
lucky 7 = "LUCKY NUMBER SEVEN!"   
lucky x = "Sorry, you're out of luck, pal!"  
```
第一行定义了函数的类型声明，参数a类型属于Intergral类型类，后两行定义了函数体。<!--more-->
所谓的模式匹配，就是haskell会将输入的数据来匹配等号的左边（模式），然后找到对应的函数体定义。
其实有点像if else是不是？
## 门卫
>模式用来检查一个值是否合适并从中取值，而门卫（guard）则用来检查一个值的某项属性是否为真。咋一听有点像是if语句，实际上也正是如此。不过处理多个条件分支时门卫的可读性要高些，并且与模式匹配契合的很好。

依然是先看代码
```haskell
bmiTell :: (RealFloat a) => a -> String   
bmiTell bmi   
    | bmi <= 18.5 = "You're underweight, you emo, you!"   
    | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"   
    | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"   
    | otherwise   = "You're a whale, congratulations!"  
```
(╯°Д°)╯︵ ┻━┻不就是在模式下又开了个if else吗
其实还是有区别的，有时候模式匹配单独无法有效描述一个函数，于是可以引入guard来匹配，只要左边的表达式为ture，则guard的值将被使用。
至于更详细的区别，看这里：
>其实，他们是完全不同的！至少在Haskell，无论如何。 警卫都更简单，更灵活的：他们基本上只是特殊的语法，转化为一系列的if / then表达式。你可以把任意的布尔表达式中的guard，但他们没有做任何事情，你不能用常规做if。 模式匹配做一些额外的事情：他们是解构数据的唯一途径 CodeGo.net，他们结合自己的范围之内的标识符。在这个意义上，guard等同于if表达式模式匹配是相当于case表达式。声明（无论是在顶层，或像let表达式）也是模式匹配表单，与“正常”的定义是与琐碎的图案，一个标识符匹配。 模式匹配的也往往是主要方式的东西居然会发生在Haskell-试图解构中的数据模式的强制评估的几件事情之一。 

## WHERE
```haskell
bmiTell :: (RealFloat a) => a -> a -> String   
bmiTell weight height   
    | weight / height ^ 2 <= 18.5 = "You're underweight, you emo, you!"   
    | weight / height ^ 2 <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"   
    | weight / height ^ 2 <= 30.0 = "You're fat! Lose some weight, fatty!"  
    | otherwise                   = "You're a whale, congratulations!"
```
可以看到weight / height ^ 2重复了很多次，于是我们可以这么写
```haskell
bmiTell :: (RealFloat a) => a -> a -> String   
bmiTell weight height   
    | bmi <= 18.5 = "You're underweight, you emo, you!"   
    | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"   
    | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"   
    | otherwise   = "You're a whale, congratulations!"   
    where bmi = weight / height ^ 2
```
## LET
let的格式为let [bindings] in [expressions]。并且let本身也是一个表达式
```haskell
cylinder :: (RealFloat a) => a -> a -> a   
cylinder r h =  
    let sideArea = 2 * pi * r * h   
        topArea = pi * r ^2   
    in  sideArea + 2 * topArea  
```
List Comprehension中也可以使用let
```haskell
calcBmis :: (RealFloat a) => [(a, a)] -> [a]   
calcBmis xs = [bmi | (w, h) <- xs, let bmi = w / h ^ 2, bmi >= 25.0]
```
可以看到我们直接忽略了in，因为名字的可见性已经预先定义好了
## CASE
其实模式匹配本质上不过就是case语句的语法糖而已。这两段代码就是完全等价的：
```haskell
head' :: [a] -> a   
head' [] = error "No head for empty lists!"   
head' (x:_) = x  
```
```haskell
head' :: [a] -> a   
head' xs = case xs of [] -> error "No head for empty lists!"   
                      (x:_) -> x  
```