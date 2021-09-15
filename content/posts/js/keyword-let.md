---
title: "关于let的两三事"
date: 2021-09-14T22:52:59+08:00
draft: false
tags: ["JavaScript", "Fontend"]
---

## 一、从定义角度出发

> let 语句声明一个块级作用域的本地变量，并且可选的将其初始化为一个值。
>
> var 声明语句声明一个变量，并可选地将其初始化为一个值。

对比[关键字let](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/let)和[关键字var](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/var)的定义可以看出， `let`强调了**块级作用域** 的 **本地变量**, 那么下面尝试从 `块级作用域` 和 `本地变量` 度看看`let`带来了什么。

### 块作用域

从一个的例子看区别：

```JavaScript
letFunc = function(){
    { 
        let foo = "let block";
    }
    console.log(foo);
}

varFunc = function() {
    { 
        var foo = "var block";
    }
    console.log(foo);
}

// exception: Uncaught ReferenceError: foo is not defined
letFunc();

// output: "var block"
varFunc();
```

`let`变量的作用域被限制到了最近的代码块中，而`var`却能从函数内部的代码块“逃逸”出来，作用域变成函数级别。

### 本地变量

`let` 不会给全局对象创建属性，而`var`会！

```JavaScript
var varName = "foo";
let letName = "bar";
// output: "foo"
console.log(this.varName);
// undefined
console.log(this.letName);
```

这点和隐式全局变量（未使用关键字`var` 或 `let`直接创建的变量）是一样的， 隐式变量也会给全局对象添加属性。

## 二、从初始化时间点出发

> 与通过  var 声明的有初始化值 undefined 的变量不同，通过 let 声明的变量直到它们的定义被执行时才初始化。

`var`变量的存在一个 [hoisting的概念](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/var#%E5%8F%98%E9%87%8F%E6%8F%90%E5%8D%87)，变量可以在声明之前被使用，除了值为`undefined`外，不会引起错误。  
而`let`则不存在hoisting，在函数内如果在声明之前被使用，会引起`Reference err`

```JavaScript
function foo() {
    console.log(varVariable);
    console.log(letVariable);
    var varVariable="var";
    let letVariable = "let";
}

// undefined
// exception: Uncaught ReferenceError: Cannot access 'letVariable' before initializatio
foo();
```

有一个特殊的场景值得提一下， `let`是直到声明才能正常的引用，且在识别变量时，拥有块级作用域的`let`的优先级会更好，这个会导致一些乍看上去奇怪的行为：

``` JavaScript
let name = "out";
function foo() {
    console.log(name);
    let name = "inner";
}

// Uncaught ReferenceError: Cannot access 'name' before initialization
foo();
```

上面这个例子中，`foo`函数内的变量name被优先识别为let变量，索引不到外部的全局变量name;

这一点结合if 或 for 这样的语法结构会让人感觉到迷惑：

``` JavaScript
function test(){
   var foo = 33;
   if (foo) {
      let foo = (foo + 55); // ReferenceError
   }
}
test();
```

if语句块内部，foo被认为是`let`变量，声明+赋值表达式 执行顺序为： 赋值表达式先执行，声明后执行。 赋值表达式中使用了还未什么的变量导致引用错误。

## 三、结束语

> "简单说两句” —— 往往不止两句

说好的两三事，好像说得已经超过了

最后贴上 `let`引入的原因和相关背景在StackOverflow讨论作为结束：
[Why was the name 'let' chosen for block-scoped variable declarations in JavaScript?](https://stackoverflow.com/questions/37916940/why-was-the-name-let-chosen-for-block-scoped-variable-declarations-in-javascri)
