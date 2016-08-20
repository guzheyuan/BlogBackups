---
layout: post
title: HASKELL学习笔记（四）
date: 2016-08-20 21:35
tags:
- haskell
---
## 递归
递归的套路无非就是先定义一个边界条件，再定义个函数，让它从一堆元素中取一个并做点事情后，把余下的元素重新交给这个函数。
来看一段快排的代码<!--more-->
```haskell
quicksort :: (Ord a) => [a] -> [a]   
quicksort [] = []   
quicksort (x:xs) =   
  let smallerSorted = quicksort [a | a <- xs, a <= x]  
       biggerSorted = quicksort [a | a <- xs, a > x]   
  in smallerSorted ++ [x] ++ biggerSorted
```

## 柯里函数
当函数可以作为参数和返回值传来传去的时候，会产生很多有趣的特性，比如，在haskell中，所有函数都可以看作只有一个参数，所有多个参数的函数都是柯里函数
比如：
```haskell
ghci> max 4 5 
5 
ghci> (max 4) 5 
5
```
两者是完全等价的，max 4返回了一个函数，这个函数返回4与参数之间较大的那个

## 高阶函数
就是参数是函数的函数
这里介绍map和filter
map取一个函数和List做参数，遍历该List的每个元素来调用该函数产生一个新的List。看下它的类型声明和实现:
```haskell
map :: (a -> b) -> [a] -> [b]   
map _ [] = []   
map f (x:xs) = f x : map f xs
```

filter函数取一个限制条件和一个List，返回该List中所有符合该条件的元素。 它的类型声明及实现大致如下:
```haskell
filter :: (a -> Bool) -> [a] -> [a]   
filter _ [] = []   
filter p (x:xs)    
    | p x       = x : filter p xs   
    | otherwise = filter p xs
```

## lambda
lambda就是匿名函数。就是用完一次就扔所以也不需要名字的函数（误）