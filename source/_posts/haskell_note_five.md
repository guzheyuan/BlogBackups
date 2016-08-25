---
layout: post
title: HASKELL学习笔记（五）
date: 2016-08-24 10:49
tags:
- haskell
---
## 折叠
所有遍历list中元素并据此返回一个值的操作都可以交给fold实现。无论何时需要遍历list并返回某值，都可以尝试下fold。因此，fold的地位可以说与map和filter并驾齐驱，同为函数式编程中最常用的函数之一。
<!--more-->
用左折叠实现的累加：
```haskell
sum' :: (Num a) => [a] -> a   
sum' xs = foldl (\acc x -> acc + x) 0 xs
```
foldl第一个参数为一个函数，这个函数的参数分别是累加值和list里的下一个值
foldr类似，参数分别为 处理函数 list 累加值
foldl1与foldr1与上面两个类似，只不过不需要初始值，list第一个就是初始值并从第二个开始折叠

## $函数调用符
```haskell
($) :: (a -> b) -> a -> b   
f $ x = f x
```
这东西有什么用？
>用空格的函数调用符是左结合的，如f a b c与((f a) b) c等价，而$则是右结合的,并且拥有最低的优先级

其实就是拿来减少括号的
比如：
sum (map sqrt [1..130])，我们可以将其改为sum $ map sqrt [1..130]

## 函数组合
haskell中的函数组合与之很像，即.函数。其定义为：
```haskell
(.) :: (b -> c) -> (a -> b) -> a -> c   
f . g = \x -> f (g x)
```

现在我们写一个求小于10000的所有奇数的平方的和的函数。
如下就是将其置于一个函数中的样子：
```haskell
oddSquareSum :: Integer   
oddSquareSum = sum (takeWhile (<10000) (filter odd (map (^2) [1..])))
```
函数组合的写法：
```haskell
oddSquareSum :: Integer   
oddSquareSum = sum . takeWhile (<10000) . filter odd . map (^2) $ [1..]
```
给人看的写法：
```haskell
oddSquareSum :: Integer   
oddSquareSum =    
    let oddSquares = filter odd $ map (^2) [1..]   
        belowLimit = takeWhile (<10000) oddSquares   
    in  sum belowLimit
```

