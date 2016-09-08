---
layout: post
title: Ninety Nine Haskell Problems（二）
date: 2016-09-05 15:28
tags:
- haskell
---
题目源于这里https://wiki.haskell.org/H-99:_Ninety-Nine_Haskell_Problems
作为学习haskell的练习补充
<!--more-->
# Problem 11
## 题目：
>难度：*
> Modified run-length encoding.
Modify the result of problem 10 in such a way that if an element has no duplicates it is simply copied into the result list. Only elements with duplicates are transferred as (N E) lists.

## 解题思路：
将a或者（int a）转换成新的数据结构

## 代码：
```haskell
data ListItem a = Single a | Multiple Int a 
    deriving (Show)

encode :: (Eq a) => [a] -> [(Int,a)]
encode xs = map (\e -> (length e,head e)) (foldl func [] $ xs)
    where func [] e = [[e]]
          func acc e
            | (head (last acc)) == e = (init acc) ++ [(last acc) ++ [e]] 
            | otherwise = acc ++ [[e]]


funcHelper :: (Int, a) -> ListItem a
funcHelper (c,x)
    | (c == 1) = Single x
    | otherwise = Multiple c x


encodeModified :: Eq a => [a] -> [ListItem a]
encodeModified xs = map funcHelper $ encode xs
```

## 运行结果
```haskell
*Main> encodeModified "aab"
[Multiple 2 'a',Single 'b']
```

# Problem 12 
## 题目：
>难度：**
>Decode a run-length encoded list.
Given a run-length code list generated as specified in problem 11. Construct its uncompressed version.

给一个problem11中不定长的list，转化为未压缩的格式

## 解题思路：
foldl遍历还原

## 代码：
```haskell
data ListItem a = Single a | Multiple Int a 
    deriving (Show)

encode :: (Eq a) => [a] -> [(Int,a)]
encode xs = map (\e -> (length e,head e)) (foldl func [] $ xs)
    where func [] e = [[e]]
          func acc e
            | (head (last acc)) == e = (init acc) ++ [(last acc) ++ [e]] 
            | otherwise = acc ++ [[e]]


funcHelper :: (Int, a) -> ListItem a
funcHelper (c,x)
    | (c == 1) = Single x
    | otherwise = Multiple c x


encodeModified :: Eq a => [a] -> [ListItem a]
encodeModified xs = map funcHelper $ encode xs


decodeModified :: Eq a => [ListItem a] -> [a]
decodeModified = foldl (\acc e -> case e of Single x -> acc ++ [x]; Multiple n x -> acc ++ replicate n x) []
```

## 运行结果
```haskell
*Main> decodeModified . decodeModified $ "aaacjdndjjjddns"
"aaacjdndjjjddns"
```

# Problem 13 
## 题目：
>难度：*
>Run-length encoding of a list (direct solution).
Implement the so-called run-length encoding data compression method directly. I.e. don't explicitly create the sublists containing the duplicates, as in problem 9, but only count them. As in problem P11, simplify the result list by replacing the singleton lists (1 X) by X.


## 解题思路：
同第9,11题，只不过单个的x转换为（1，x），因此可以直接（Int，x）-> ListItem

## 代码：
```haskell
data ListItem a = Single a | Multiple Int a 
    deriving (Show)

encode' :: Eq a => [a] -> [(Int,a)]
encode' = foldr helper []
    where
      helper x [] = [(1,x)]
      helper x (y@(a,b):ys)
        | x == b    = (1+a,x):ys
        | otherwise = (1,x):y:ys


encodeDirect :: Eq a => [a] -> [ListItem a]
encodeDirect = map encodeHelper . encode'
    where
      encodeHelper (1,x) = Single x
      encodeHelper (n,x) = Multiple n x
```

## 运行结果
```haskell
*Main> encodeDirect "aab"
[Multiple 2 'a',Single 'b']
```

# Problem 14
## 题目：
>难度：*
> Duplicate the elements of a list.

把list里的元素重复一下

## 解题思路：
用fold遍历一遍

## 代码：
```haskell
dupli :: [a] -> [a]
dupli xs = foldl (\acc e -> acc ++ [e,e]) [] xs
```

## 运行结果
```haskell
*Main> dupli "aab"
"aaaabb"
```

# Problem 15
## 题目：
>难度：*
>Replicate the elements of a list a given number of times.

重复一个list中的元素n次

## 解题思路：
类似14题

## 代码：
```haskell
dupli :: [a] -> Int -> [a]
dupli xs n = foldl (\acc e -> acc ++ replicate n e) [] xs
```

## 运行结果
```haskell
*Main> dupli "aab" 3
"aaaaaabbb"
```

# Problem 16
## 题目：
>难度：**
>Drop every N'th element from a list.

把list中的第n个元素去掉

## 解题思路：
myDrop (x:xs) n = x : myDrop xs (n - 1) 

## 代码：
```haskell
myDrop :: [a] -> Int -> [a]
myDrop [] _ = []
myDrop (x:xs) 1 = xs
myDrop (x:xs) n = x : myDrop xs (n - 1)  
```

## 运行结果
```haskell
*Main> myDrop "asdfghjkl" 3
"asfghjkl"
```

# Problem 17
## 题目：
>难度：*
>Split a list into two parts; the length of the first part is given.
Do not use any predefined predicates.

## 解题思路：
拆为两个问题：一个求前半段，另一个求后半段

## 代码：
```haskell
before :: [a] -> Int -> [a]
before [] _ = []
before xs 0 = []
before (x:xs) n = x : before xs (n-1) 

after :: [a] -> Int -> [a]
after [] _ = []
after xs 0 = xs
after (x:xs) n = after xs (n-1) 

mySplit :: [a] -> Int -> ([a],[a])
mySplit [] _ = ([],[])
mySplit xs n = (before xs n, after xs n)
```

## 运行结果
```haskell
*Main> mySplit "asdfghjkl" 3
("asd","fghjkl")
```

# Problem 18
## 题目：
>难度：**
> Extract a slice from a list.
Given two indices, i and k, the slice is the list containing the elements between the i'th and k'th element of the original list (both limits included). Start counting the elements with 1.

list切片

## 解题思路：
组合take和drop

## 代码：
```haskell
mySlice :: [a] -> Int -> Int -> [a]
mySlice xs s e = take (e - s + 1) $ drop (s - 1) xs 
```

## 运行结果
```haskell
*Main> mySlice "123456789" 3 7
"34567"
```

# Problem 19
## 题目：
>难度：**
>Rotate a list N places to the left.

把一个list左旋n位

## 解题思路：
见代码

## 代码：
```haskell
myRotate :: [a] -> Int -> [a]
myRotate xs n = drop n xs ++ take n xs
```

## 运行结果
```haskell
*Main> myRotate "123456789" 3
"456789123"
```

# Problem 20
## 题目：
>难度：*
>Remove the K'th element from a list

把list里的第k位移出来

## 解题思路：
同16题

## 代码：
```haskell
removeAt :: [a] -> Int -> (a,[a])
removeAt xs n = (xs!!(n-1), helpFunc xs n)
    where helpFunc [] _ = []
          helpFunc (x:xs) 1 = xs
          helpFunc (x:xs) n = x : helpFunc xs (n - 1)  
```

## 运行结果
```haskell
*Main> removeAt "123456789" 3
('3',"12456789")
```