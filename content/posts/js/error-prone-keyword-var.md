---
title: "易错的关键字var"
date: 2021-09-09T23:07:28+08:00
draft: false
tags: ["JavaScript", "Fontend"]
---

”易错的关键字var“这个标题起得很唬人，其实是作者自己的感受；对于深耕于JavaScript的专家，可能并不这么看。这里并不想引战，这只是作为一个刚开始了解JavaScript的我的认识。

首先需要说明的是`var`关键字的知识源，我通过MDN在线文字教程了解JavaScript，关于JavaScript `var`关键字的理解来源于: [MDN var 描述](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/var)。

注：下述观点都是在JavaScript运行在非严格模式下; 样例程序结果在nodejs v16.6.1下运行得到, html样例在Chrome 92.0.4515.159 (Official Build) (x86_64) 下运行获得。

## 易错点一： 重复声明

使用var重复声明一个变量不会引起错误，更甚至重复声明是不会丢失变量之前的值。

```JavaScript
var foo = "declare 1st";
var foo;
// output: declare 1st
console.log(foo);
```

这一点有悖于大部分编程语言（比如java、golang、C），但在不同的作用域的相同变量行为又不符合这个行为，变成了其它语言的变量覆盖：

```JavaScript
var foo = "outside";
function bar() {
    var foo;
    // output: undefined
    console.log(foo);
    foo="modify inside";
    // output: "modify inside"
    console.log(foo);
}
// output: "outside"
console.log(foo);
bar();
// output: "outside"
console.log(foo);
```

即我们可以得到这样一个结论： **变量重复声明且不丢失值只能发生在相同作用域内， 不同作用域的同名变量仍然满足变量覆盖。**

造成这样行为差异的原因需要进一步了解，相信随着学习的深入后面能找到答案！

## 易错点二： 不声明变量直接赋值的变量的作用域范围提升

> "当赋值给未声明的变量, 则执行赋值后, 该变量会被隐式地创建为全局变量"

这个特性造成的行为让人感到很困惑、很容易出错：函数内部居然能定义一个全局变量，就好像变量逃逸出函数作用域：

```JavaScript
function bar() {
    foo="assign inside";
}

// exception: Uncaught ReferenceError: foo is not defined
console.log(foo);
bar()
// output: "assign inside"
console.log(foo);
```

结合重复声明，下面这段程序会导致变量被错误的修改：

```JavaScript
function bar() {
    // 假设的bar函数的编写者不知道外部有个变量叫foo， 隐式定义的全局变量会被错误的修改。
    foo = "assign inside";
}

// exception: Uncaught ReferenceError: foo is not defined
console.log(foo);

bar();
var foo = "outside";
// output: "outside"
console.log(foo);
```

同时未声明变量能被删除，声明变量则不能。

## 易错点三： hoisting(变量提升）

> "在代码中的任意位置声明变量总是等效于在代码开头声明。这意味着变量可以在声明之前使用，这个行为叫做“hoisting”"

看下面这个例子，需要在网页中执行

```html
<!DOCTYPE html>
<html>

<head>
    <script>
        function bar() {
            foo = "inside";
            // output: "inside"
            console.log(foo);
            var foo;
        }
        bar();
        // exception: Uncaught ReferenceError: foo is not defined
        console.log(foo);
    </script>
</head>

<body>
</body>

</html>
```

同一个作用域内先赋值在声明并不会造成变量作用域提升(易错点二)， 而是好像解释器能识别这种场景，把声明“调整”到赋值前，让人迷惑。

## 解决var易错的“银弹”

> "No Silver Bullet"
>
> —— Essence and Accidents of Software Engineering

要想不踩入`var`这些易错的坑里的方法就是： **完全不要使用var** ——这是一个对自己的忠告，一个JavaScript领域的银弹（至少现在我是这么认为的）。

用关键字`let`代替所有使用`var`的地方， 正如MDN所建议的这样：
> "我们建议您在代码中尽可能多地使用 let，而不是 var。因为没有理由使用 var，除非您需要用代码支持旧版本的 Internet Explorer (它直到第 11 版才支持 let ，现代的 Windows Edge 浏览器支持的很好)。"

关于let的参考：[MDN let描述](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/let)

let变量的特点也可以拿出来说一说，但那还是放到另一篇文章将比较合适。
