---
layout: post
title: "（译）Haskell中的WHNF"
date: 2016-09-22 15:31
tags:
- haskell
---
在学习《real world Haskell》时对NF、WHNF的概念有些困惑，于是去stackoverflow查了查。本文来源于stackoverflow上的一个提问，大意就是怎么理解Haskell中的WHNF、HNF以及NF。译文是该提问下票数最高的回答。
<!--more-->
原文地址：[链接](http://stackoverflow.com/questions/6872898/haskell-what-is-weak-head-normal-form)

---
>I'll try to give an explanation in simple terms. As others have pointed out, head normal form does not apply to Haskell, so I will not consider it here.

我将试着用一些简单的术语来解释它们。另外，由于Haskell中并没有用到HNF，所以在这里我就不解释了。

## Normal form
## 范式

>An expression in normal form is fully evaluated, and no sub-expression could be evaluated any further (i.e. it contains no un-evaluated thunks).
These expressions are all in normal form:
```
42
(2, "hello")
\x -> (x + 1)
```

一个NF的表达式是可以完全被求值的，并且不存在需要进一步求值的子表达式（包括不可求值的部分）。
这些表达是都是NF的：

```
42
(2, "hello")
\x -> (x + 1)
```

>These expressions are not in normal form:
```
1 + 2                 -- we could evaluate this to 3
(\x -> x + 1) 2       -- we could apply the function
"he" ++ "llo"         -- we could apply the (++)
(1 + 1, 2 + 2)        -- we could evaluate 1 + 1 and 2 + 2
```

这些表达式都不是NF的：

```
1 + 2                 -- 我们可以把它求值为 3
(\x -> x + 1) 2       -- 我们可以应用这个函数
"he" ++ "llo"         -- 我们可以应用 (++)
(1 + 1, 2 + 2)        -- 我们可以对 1 + 1 以及 2 + 2求值
```

## Weak head normal form
## 弱头范式

>An expression in weak head normal form has been evaluated to the outermost data constructor or lambda abstraction (the *head*). Sub-expressions *may or may not have been evaluated*. Therefore, every normal form expression is also in weak head normal form, though the opposite does not hold in general.

我们说一个表达式是WHNF（弱头范式）的，意味着它可以被求值为最外层的值构造器或匿名函数（lambda）抽象（所谓的头*head*）。子表达式或许不能被求值。因此，所有NF表达式都满足WHNF，尽管反对方一般不这么认为。

>To determine whether an expression is in weak head normal form, we only have to look at the outermost part of the expression. If it's a data constructor or a lambda, it's in weak head normal form. If it's a function application, it's not.

判断一个表达式是否满足WHNF，我们只需要看最外层的表达式。如果是一个值构造器或一个匿名函数，那它就满足WHNF。如果是一个函数调用，那它就不是。

>These expressions are in weak head normal form:
```
(1 + 1, 2 + 2)       -- the outermost part is the data constructor (,)
\x -> 2 + 2          -- the outermost part is a lambda abstraction
'h' : ("e" ++ "llo") -- the outermost part is the data constructor (:)
```

这些表达式是满足WHNF的：

```
(1 + 1, 2 + 2) -- 最外层是一个值构造器 (,)
\x -> 2 + 2 -- 最外层是一个匿名函数的抽象
'h' : ("e" ++ "llo") -- 最外层是一个值构造器 (:)
```

>As mentioned, all the normal form expressions listed above are also in weak head normal form.
These expressions are not in weak head normal form:
```
1 + 2                -- the outermost part here is an application of (+)
(\x -> x + 1) 2      -- the outermost part is an application of (\x -> x + 1)
"he" ++ "llo"        -- the outermost part is an application of (++)
```

就像之前所提到的，所有以上列出的NF表达式都是符合WHNF的。以下表达式不符合WHNF：

```
1 + 2 -- 最外层是 (+) 函数的调用
(\x -> x + 1) 2 -- 最外层是匿名函数 (\x -> x + 1) 的调用
"he" ++ "llo" -- 最外层是 (++) 函数的调用
```

## Stack overflows
## 栈溢出

>Evaluating an expression to weak head normal form may require that other expressions be evaluated to WHNF first. For example, to evaluate 1 + (2 + 3) to WHNF, we first have to evaluate 2 + 3. If evaluating a single expression leads to too many of these nested evaluations, the result is a stack overflow.

把一个表达式转换为WHNF可能需要先对其他表达式求值使其成为WHNF。举个栗子，把1+（2+3）转化为WHNF，我们首先必须要对2+3求值。如果一个表达式的求值需要对许多内嵌的（子表达式）求值，那么将会导致栈溢出。

>This happens when you build up a large expression that does not produce any data constructors or lambdas until a large part of it has been evaluated. These are often caused by this kind of usage of foldl:
```
foldl (+) 0 [1, 2, 3, 4, 5, 6] 
= foldl (+) (0 + 1) [2, 3, 4, 5, 6] 
= foldl (+) ((0 + 1) + 2) [3, 4, 5, 6] 
= foldl (+) (((0 + 1) + 2) + 3) [4, 5, 6] 
= foldl (+) ((((0 + 1) + 2) + 3) + 4) [5, 6] 
= foldl (+) (((((0 + 1) + 2) + 3) + 4) + 5) [6] 
= foldl (+) ((((((0 + 1) + 2) + 3) + 4) + 5) + 6) [] 
= (((((0 + 1) + 2) + 3) + 4) + 5) + 6 
= ((((1 + 2) + 3) + 4) + 5) + 6 
= (((3 + 3) + 4) + 5) + 6 
= ((6 + 4) + 5) + 6 
= (10 + 5) + 6 
= 15 + 6 
= 21
```

当你展开一个大型表达式时，不会打开任何值构造器或lambda表达式，直到它的其他部分都已被求值。foldl函数经常被这样使用：

```
foldl (+) 0 [1, 2, 3, 4, 5, 6] 
= foldl (+) (0 + 1) [2, 3, 4, 5, 6] 
= foldl (+) ((0 + 1) + 2) [3, 4, 5, 6] 
= foldl (+) (((0 + 1) + 2) + 3) [4, 5, 6] 
= foldl (+) ((((0 + 1) + 2) + 3) + 4) [5, 6] 
= foldl (+) (((((0 + 1) + 2) + 3) + 4) + 5) [6] 
= foldl (+) ((((((0 + 1) + 2) + 3) + 4) + 5) + 6) [] 
= (((((0 + 1) + 2) + 3) + 4) + 5) + 6 
= ((((1 + 2) + 3) + 4) + 5) + 6 
= (((3 + 3) + 4) + 5) + 6 
= ((6 + 4) + 5) + 6 
= (10 + 5) + 6 
= 15 + 6 
= 21
```

>Notice how it has to go quite deep before it can get the expression into weak head normal form.

我们注意到，在表达式转换为WHNF之前，它嵌套的非常之深。

>You may wonder, why does not Haskell reduce the inner expressions ahead of time? That is because of Haskell's laziness. Since it cannot be assumed in general that every subexpression will be needed, expressions are evaluated from the outside in.

你可能会奇怪，为什么Haskell不提前减少内嵌的表达式呢？因为Haskell是惰性的。一般情况下你并不能假定所有子表达式都需要被求值，表达式的求值是由外而内的。

>(GHC has a strictness analyzer that will detect some situations where a subexpression is always needed and it can then evaluate it ahead of time. This is only an optimization, however, and you should not rely on it to save you from overflows).

（GHC有一个严格的分析，它会检测哪些子表达式是必需的，然后提前计算它。但那只是一个优化，你不应该指望它来保证你的代码免于栈溢出）

>This kind of expression, on the other hand, is completely safe:
```
data List a = Cons a (List a) | Nil
foldr Cons Nil [1, 2, 3, 4, 5, 6]
 = Cons 1 (foldr Cons Nil [2, 3, 4, 5, 6])  -- Cons is a constructor, stop. 
```

换句话说，这类表达式是完全安全的：

```
data List a = Cons a (List a) | Nil
foldr Cons Nil [1, 2, 3, 4, 5, 6]
 = Cons 1 (foldr Cons Nil [2, 3, 4, 5, 6]) -- Cons 是一个构造器, 停止. 
```

>To avoid building these large expressions when we know all the subexpressions will have to be evaluated, we want to force the inner parts to be evaluated ahead of time.

为了避免构建这类巨大的表达式，当我们知道所有子表达式都将会被求值时，我们必须强制提前对内部进行求值。


## seq
## seq函数

>*seq* is a special function that is used to force expressions to be evaluated. Its semantics are that *seq x y* means that whenever *y* is evaluated to weak head normal form, x is also evaluated to weak head normal form.It is among other places used in the definition of foldl', the strict variant of foldl.
```
foldl' f a [] = a
foldl' f a (x:xs) = let a' = f a x in a' `seq` foldl' f a' xs
```

seq是一个特殊的函数，它用来强制表达式提前被求值。seq x y意味着无论是否y能被求值为WHNF，x都会被转换为WHNF。它在其他地方被使用，例如在foldl'函数（foldl的一个变体）的定义中
```
foldl' f a [] = a
foldl' f a (x:xs) = let a' = f a x in a' `seq` foldl' f a' xs
```

## Each iteration of foldl'
## foldl' 的每一次迭代

forces the accumulator to WHNF. It therefore avoids building up a large expression, and it therefore avoids overflowing the stack.
```
foldl' (+) 0 [1, 2, 3, 4, 5, 6] 
= foldl' (+) 1 [2, 3, 4, 5, 6] 
= foldl' (+) 3 [3, 4, 5, 6] 
= foldl' (+) 6 [4, 5, 6] 
= foldl' (+) 10 [5, 6] 
= foldl' (+) 15 [6] 
= foldl' (+) 21 [] 
= 21 -- 21 is a data constructor, stop.
```

强制把累加器转换为WHNF。因此它可以避免构建一个巨大的表达式，也因此避免了栈溢出。
```
foldl' (+) 0 [1, 2, 3, 4, 5, 6] 
= foldl' (+) 1 [2, 3, 4, 5, 6] 
= foldl' (+) 3 [3, 4, 5, 6] 
= foldl' (+) 6 [4, 5, 6] 
= foldl' (+) 10 [5, 6] 
= foldl' (+) 15 [6] 
= foldl' (+) 21 [] 
= 21 -- 21 是一个值构造器, 停止.
```

But as the example on HaskellWiki mentions, this does not save you in all cases, as the accumulator is only evaluated to WHNF. In the example, the accumulator is a tuple, so it will only force evaluation of the tuple constructor, and not acc or len.
```
f (acc, len) x = (acc + x, len + 1)

foldl' f (0, 0) [1, 2, 3]
 = foldl' f (0 + 1, 0 + 1) [2, 3]
 = foldl' f ((0 + 1) + 2, (0 + 1) + 1) [3]
 = foldl' f (((0 + 1) + 2) + 3, ((0 + 1) + 1) + 1) []
 = (((0 + 1) + 2) + 3, ((0 + 1) + 1) + 1) -- tuple constructor, stop.
```

但是Haskell维基中提到的一个例子，并不是所有情况都能由此解决，当累加器可以求值为WHNF时。比如在那个例子中，累加器是一个元组，因此，它只强制求值到元组的构造器，而不是其中的acc和len。

```
f (acc, len) x = (acc + x, len + 1)

foldl' f (0, 0) [1, 2, 3]
 = foldl' f (0 + 1, 0 + 1) [2, 3]
 = foldl' f ((0 + 1) + 2, (0 + 1) + 1) [3]
 = foldl' f (((0 + 1) + 2) + 3, ((0 + 1) + 1) + 1) []
 = (((0 + 1) + 2) + 3, ((0 + 1) + 1) + 1) -- 元组的构造器, 停止.
```

>To avoid this, we must make it so that evaluating the tuple constructor forces evaluation of acc
 and len. We do this by using seq.
```
f' (acc, len) x = let acc' = acc + x
                      len' = len + 1
                  in  acc' `seq` len' `seq` (acc', len')
foldl' f' (0, 0) [1, 2, 3]
 = foldl' f' (1, 1) [2, 3]
 = foldl' f' (3, 2) [3]
 = foldl' f' (6, 3) []
 = (6, 3)                    -- tuple constructor, stop.
```

为了避免这种情况，我们必须强制对元组构造器的求值，然后强制对acc和len求值。我们可以这样使用seq：
```
f' (acc, len) x = let acc' = acc + x
                      len' = len + 1
                  in  acc' `seq` len' `seq` (acc', len')
foldl' f' (0, 0) [1, 2, 3]
 = foldl' f' (1, 1) [2, 3]
 = foldl' f' (3, 2) [3]
 = foldl' f' (6, 3) []
 = (6, 3)                    -- 元组构造器，停止.
```
