---
layout: post
title: "async源码学习（一）：waterfall"
date: 2016-11-20 3:23
tags:
- JavaScript
---
某天写完nodejs，忽然福至心灵想去看看async的源码长什么样子，打开一看原来就1000来行，非常简洁。于是一边看一边写下这篇文章，作为学习笔记记录下来。
<!--more-->

# 关于waterfall

>Runs the tasks array of functions in series, each passing their results to the next in the array. However, if any of the tasks pass an error to their own callback, the next function is not executed, and the maincallback is immediately called with the error.

按顺序依次执行一组函数。每个函数产生的结果依次传给数组中的下一个函数。如果有一个有任何一个函数的产生了错误，那么它的下一个函数就不会执行，而是执行最后的回调函数（maincallback，就是第二个参数）来处理这个错误。

waterfall的参数如下：

|Name        |Type         |Description|
|:------------- |:-------------|:---------------|
|tasks         |Array        |An array of functions to run, each function is passed a callback(err, result1, result2, ...) it must call on completion. The first argument is an error (which can be null) and any further arguments will be passed as arguments in order to the next task.
|callback    |function <optional>|An optional callback to run once all the functions have completed. This will be passed the results of the last task's callback. Invoked with (err, [results]).


# waterfall源码
由于是直接看的手上项目里的源码，所以版本比较老，最新版貌似已经把每个方法都拆成一个个小模块了。先将就看着，等有机会在把最新的补上，比较下看看有什么不同：）

```JavaScript
async.waterfall = function (tasks, callback) {
    // 一些边界情况的处理
    // 参数callback未定义时，默认执行一个空的函数
    callback = callback || function () {};
    // 传入参数tasks不是数组时报错
    if (tasks.constructor !== Array) {
      var err = new Error('First argument to waterfall must be an array of functions');
      return callback(err);
    }
    // 空数组直接调用callback并返回
    if (!tasks.length) {
        return callback();
    }
    // 定义了wrapIterator函数
    var wrapIterator = function (iterator) {
        // 此处省略若干行
    };
    // 执行wranpIterator返回的函数
    wrapIterator(async.iterator(tasks))();
};
```

这里大约可以分为3部分，第一段是对一些边界条件的处理，第二段定义了一个函数wrapIterator对iterator进行了一层包装，第三段执行包装后的wrapIterator。

为了能够更好的理解waterfall的工作流程，我们先来看看async.iterator()这个函数

```
async.iterator = function (tasks) {
    // makeCallback相当于对每个tasks里的函数进行一层包装
    // 包装后的函数多了一个next属性
    var makeCallback = function (index) {
        // 定义一个函数对象fn
        // 对象结构为{ [Function: fn] next: [Function] }
        var fn = function () {
            // fn执行时会执行task[index],并将fn的参数传给tasks[index]
            if (tasks.length) {
                tasks[index].apply(null, arguments);
            }
            // fn执行完以后会返回tasks中的下个函数
            return fn.next();
        };
        // next指向makeCallback包装后的task[index + 1]
        fn.next = function () {
            return (index < tasks.length - 1) ? makeCallback(index + 1): null;
        };
        // fn就是包装后的tasks[index]
        return fn;
    };
    // 返回第一个fn，于是我们得到了一个迭代器
    return makeCallback(0);
};
```
每调用一次makeCallback就会包装一个task为iterator，而在fn.next指向下个iterator后又会递归的调用makeCallback，因此makeCallback(0)相当于完成了两件事，递归的把每个task包装为iterator，并返回了第一个iterator的引用。

抽象点说，iterator函数接受一个函数数组，并把它串起来以后变成了一个迭代器

用起来大致就是这样的：
```
var tasks = [
    function () {
        console.log(1);
    },
    function () {
        console.log(2);
    }
];

var iterator = async.iterator(tasks);

iterator = iterator();
iterator = iterator();
```
输出：
```console
1
2
```

现在我们可以看下wrapIterator函数了，源码如下

```
var wrapIterator = function (iterator) {
    // 如果是err，则跳转到最后的callback
    return function (err) {
        if (err) {
            callback.apply(null, arguments);
            callback = function () {};
        }
        else {
            // args是传到下个function的参数，第一个err是不需要的
            // 要注意的是arguments并不是一个array
            // 所以对arguments做切片要用如下方式
            var args = Array.prototype.slice.call(arguments, 1);
            // 下一个迭代器
            var next = iterator.next();
            // 把下一个包装后的wrapIterator作为最后个参数传入
            if (next) {
                args.push(wrapIterator(next));
            }
            else {
                args.push(callback);
            }
            // 执行iterator
            // 这个iterator会执行一遍task，并在回调处调用下一个wrapIterator
            async.setImmediate(function () {
                iterator.apply(null, args);
            });
        }
    };
};
```
这个wrapIterator声明了一个匿名函数，这个function实际上就是我们task中间用来处理err的那个“中转”了，类似一个调度器，对传入的第一个err进行辨别，如果有err则直接调最后的callback而不是下一个task。传入的iterator，就是我们要执行的下个tasks。

# 总结
waterfall的实现并不困难，有兴趣的话可以自己尝试下。源码实现的非常简洁，借用了iterator这个函数实现了一次包装，将一个数组变成了迭代器，再用一个调度器去执行迭代器。