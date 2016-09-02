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
*Main> myButLast [1,2,3,4]
3
*Main> myButLast ['a'..'z']
'y'
```

# Problem 3
## 题目：
>难度：*
>Find the K'th element of a list. The first element in the list is number 1.

寻找第k个元素

## 解题思路：
遍历list

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
*Main> elementAt [1,2,3] 2
2
*Main> elementAt "haskell" 5
'e'
```

# Problem 4
## 题目：
>难度：*
>Find the number of elements of a list.

list的长度

## 解题思路：
用折叠实现

## 代码：
```haskell
myLength :: [a] -> Int
myLength = foldl (\acc _ -> acc + 1) 0
```

## 运行结果
```haskell
*Main> myLength [123, 456, 789]
3
*Main> myLength "Hello, world!"
13
```

# Problem 5
## 题目：
>难度：*
>Reverse a list.

转置一个list

## 解题思路：
用折叠实现

## 代码：
```haskell
myReverse :: [a] -> [a]
myReverse = foldl (flip (:)) []
```

## 运行结果
```haskell
*Main> myReverse "A man, a plan, a canal, panama!"
"!amanap ,lanac a ,nalp a ,nam A"
*Main> myReverse [1,2,3,4]
[4,3,2,1]
```

# Problem 6
## 题目：
>难度：*
>Find out whether a list is a palindrome. A palindrome can be read forward or backward; e.g. (x a m a x).


## 解题思路：
递归解决

## 代码：
```haskell
isPalindrome :: (Eq a) => [a] -> Bool
isPalindrome [] = True
isPalindrome [_] = True
isPalindrome xs = (head xs) == (last xs) && (isPalindrome $ init $ tail xs)
```

## 运行结果
```haskell
*Main> isPalindrome [1,2,3]
False
*Main> isPalindrome "madamimadam"
True
*Main> isPalindrome [1,2,4,8,16,8,4,2,1]
True
```

# Problem 7
## 题目：
>难度：**
> Flatten a nested list structure.
Transform a list, possibly holding lists as elements into a `flat' list by replacing each list with its elements (recursively).

## 解题思路：
取每个头元素，把它铺平

## 代码：
```haskell
data NestedList a = Elem a | List [NestedList a]

flatten :: NestedList a -> [a]
flatten (Elem a) = [a]
flatten (List (x:xs)) = flatten x ++ (flatten $ List xs)
flatten (List []) = []
```

## 运行结果
```haskell
*Main> flatten (Elem 5)
[5]
*Main> flatten (List [Elem 1, List [Elem 2, List [Elem 3, Elem 4], Elem 5]])
[1,2,3,4,5]
*Main> flatten (List [])
[]
```

# Problem 8
## 题目：
>难度：**
>Eliminate consecutive duplicates of list elements.
If a list contains repeated elements they should be replaced with a single copy of the element. The order of the elements should not be changed.

去掉list中连续重复的元素（aaaa变为a）

## 解题思路：


## 代码：
```haskell
compress :: Eq a => [a] -> [a]
compress = foldl skip []
    where skip [] x = [x]
          skip acc x
                | x == last acc = acc
                | otherwise = acc ++ [x]
```

## 运行结果
```haskell
*Main> compress "aaaabccaadeeee"
"abcade"
```

# Problem 9
## 题目：
>难度：**
>Pack consecutive duplicates of list elements into sublists. If a list contains repeated elements they should be placed in separate sublists.

将连续相同的元素打包

## 解题思路：
foldl遍历list

## 代码：
```haskell
pack :: Eq a => [a] -> [[a]]
pack = foldl func [] 
    where func [] e = [[e]]
          func acc e
            | (head (last acc)) == e = (init acc) ++ [(last acc) ++ [e]] 
            | otherwise = acc ++ [[e]]
```

## 运行结果
```haskell
*Main> pack ['a', 'a', 'a', 'a', 'b', 'c', 'c', 'a']
["aaaa","b","cc","a"]
```

# Problem 10
## 题目：
>难度：*
>Run-length encoding of a list. Use the result of problem P09 to implement the so-called run-length encoding data compression method. Consecutive duplicates of elements are encoded as lists (N E) where N is the number of duplicates of the element E.


## 解题思路：
将上题的结果map

## 代码：
```haskell
encode :: (Eq a) => [a] -> [(Int,a)]
encode xs = map (\e -> (length e,head e)) (foldl func [] $ xs)
    where func [] e = [[e]]
          func acc e
            | (head (last acc)) == e = (init acc) ++ [(last acc) ++ [e]] 
            | otherwise = acc ++ [[e]]
```

## 运行结果
```haskell
*Main> encode "aaaabccaadeeee"
[(4,'a'),(1,'b'),(2,'c'),(2,'a'),(1,'d'),(4,'e')]
```