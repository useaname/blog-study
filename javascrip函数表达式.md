---
date: 2016-05-18 20:52
status: public
title: javascript函数表达式笔记
---

定义函数的方式有两种。一种是函数申明,另一种是函数表达式。函数申明语法是:
```javascript
    function name(arg1, arg2, arg3){
        //sth
    }
```
函数声明醉重要的一个特征就是函数声明提升(function declaration hoisting).意思是在执行代码之前会读取函数声明。这就意味着可以把函数声明放在调用它的语句后面。
```javascript
//不会抛出错误，
//因为在代码执行前会先
//读取函数声明
sayHi();
function sayHi(){ 
    console.log("Hi.");
};
```
第二种创建函数的方式是使用函数表达式。函数表达式有几种不同的语法形式。几种常见的形式:
```javascript
    var functionName = function (arg0, arg1, arg2) {
        //函数体
    }
```
这种形式好像是常规的变量复制语句，即创建一个函数并将它赋值给functionName。这种情况下创建的函数叫匿名函数(anonymous function),因为function后面没有标志符号。匿名函数有时候也叫拉姆达函数。匿名函数的name属性是空字符串。
函数表达hi与其他表达式一样，在试用前必须先赋值。
```javascript
sayHi(); //错误，函数还不存在
var sayHi = function(){
    console.log("Hi.");
}
```
解释函数提升的关键，就是理解函数声明与函数表达式之间的区别。以下代码结果可能意想不到:
```javascript
if(condition) {
    function sayHi() {
        console.log("Hi");
    }    
} else {
    function sayHi(){
        console.log("Yo");    
    }
}
```
表面上