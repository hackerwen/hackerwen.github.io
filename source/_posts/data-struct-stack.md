---
title: 栈——JavaScript实现
date: 2017-08-02
tags: [原生js]
categories: 数据结构
---

### 栈结构

#### 目的: 使用JavaSript实现栈这种数据结构

#### 定义:

1. 特殊的列表，栈内的元素只能通过列表的一端访问，栈顶

2. 后入先出(LIFO,last-in-first-out)的数据结构

#### 需要实现的方法:

1. 入栈 push

2. 出栈 pop

3. 清空栈 clear

4. 栈内元素总量查找 getLength

5. 查找 pick

#### 实现代码如下

{% codeblock lang:javascript %}
function Stack() {
    this.dataStore = [];
    this.top = 0;
}

Stack.prototype.push = function (element) { // 入栈
    this.dataStore[this.top] = element;
    this.top = this.top + 1;
}

Stack.prototype.pop = function () { // 出栈
    this.top = this.top - 1;
    return this.dataStore[this.top];
}

Stack.prototype.pick = function () { // 查找
    return this.dataStore[this.top - 1];
}

Stack.prototype.clear = function () { //清空栈
    this.top = 0;
}

Stack.prototype.getLength = function () {
    return this.top;
}

// s就是我们的栈 我们操作对象是s 不是s的dataStore,切记

var s = new Stack();

s.push("Tencent");
s.push('Ali');
s.push("Baidu");

console.log(s.pick()); // Baidu
console.log(s.pop()); // Baidu
console.log(s.pick()); // Ali
 {% endcodeblock %}  
#### 应用

1. 回文数的判断

回文就是指一个单词，数组，短语，从前往后从后往前都是一样的 12321.abcba

回文最简单的思路就是:把元素反转后如果与原始的元素相等，那么就意味着这就是一个回文了

这里可以用到这个栈类来操作

{% codeblock lang:javascript %}
// 定义栈
var Stack = require('./stack.js');

function isPalindrome(str) {

    var s = new Stack();

    for (var i = 0; i < str.length; i++) {
        s.push(str[i]);
    }

    var newStr = "";

    for (var j = 0; j < str.length; j++) {
        newStr += s.pop();
    }

    if (str === newStr) {
        return true;
    } else {
        return false;
    }
}

console.log(isPalindrome("112211"));
{% endcodeblock %}  

看看这个isPalindrome函数，其实就是通过调用Stack类，然后把传递进来的str这个元素给分解后的每一个组成单元给压入到栈了

根据栈的原理，后入先出的原则，通过pop的方法在反组装这个元素，最后比较下之前与组装后的，如果相等就是回文了

你要是用reverse也可以...

2. 将数字转换为二进制和八进制

{% codeblock lang:javascript %}
var Stack = require('./stack.js');

var numConvert = function (num, base) {
    var stack = new Stack();
    let converted = '';

    while(num > 0) {
        stack.push(num % base);
        num = Math.floor(num / base);
    }

    while(stack.getLength() > 0) {
        converted += stack.pop();
    }

    return converted;
}

console.log(numConvert(10, 2)); // 1010
{% endcodeblock %}  