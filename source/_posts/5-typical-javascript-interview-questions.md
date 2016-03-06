title: 5 個 JavaScript 面試常見問題
date: 2014-09-22 23:23:12
tags:
	- JavaScript
  - 面試
---

以下內容為閱讀 [5 Typical JavaScript Interview Exercises](http://www.sitepoint.com/5-typical-javascript-interview-exercises/) 文章後的觀後感。

這幾個問題說基本也滿基本的但是有些地方一不小心可能就會犯錯！

<!-- more -->

問題一：Scope
-------------
```js
(function() {
  var a = b = 5;
})();
 
console.log(b);
```

請問 `console.log(b);` 印出來的結果是？

**答案**
`5`

在這個 IIFE 中，變數 `a` 使用了 `var` 關鍵字來宣告成為 local variable，而 `b` 則沒有 `var` 修飾所以宣告成為 global variable。

那如何避免這個問題呢？只需要在 IIFE 中加入 `'use strict'`，如此一來只要有任何變數試圖宣告為全域變數都會出現錯誤警告。

```js
(function() {
  'use strict';
  var a = window.b = 5;
})();
 
console.log(b);
```

所以假若你還是要把 `b` 宣告為全域變數 (global variable) 則可以透過 `window.b` 來指定。


問題二：Create "native" methods
-------------------------------
擴充 String 的 method 使其能呼叫 `.repeatify(num)` 來產生重複的字串
```js
console.log('hello'.repeatify(3));
```

並且印出 `hellohellohello`

**答案**
```js
String.prototype.repeatify = String.prototype.repeatify || function(times) {
   var str = '';
 
   for (var i = 0; i < times; i++) {
      str += this;
   }
 
   return str;
};
```

這題應該就比較簡單了，直接從 `String.prototype` 下手，並使用 `String.prototype.repeatify || function(times)` 來避免覆寫已經存在的方法。

這個技巧很常用在一些 `shim` 的功能 (用來擴充本來瀏覽器沒有提供的功能)。


問題三：Hoisting
----------------
寫出以下程式碼執行結果，並說明原因。

```js
function test() {
   console.log(a);
   console.log(foo());
    
   var a = 1;
   function foo() {
      return 2;
   }
}

test();
```

**答案**
第一個 `undefined`
第二個 `2`

所有的變數 (Variable) 與 函式 (Function) 都會被往上提 hoisted 至 Function 的頂端。但是要注意的是，變數往上提**並不會一起賦值**。所以 `a` 印出來是 `undefined`，我們把上面的 code 轉換成提升後等價的 code 如下：

```js
function test() {
   var a;
   function foo() {
      return 2;
   }
 
   console.log(a);
   console.log(foo());
    
   a = 1;
}

test();
```
這樣是否更能明白了呢？其實我看這篇文章之前對於這個 `hosited` 概念十分陌生，可以參見另一篇文章 [Back to Basics: JavaScript Hoisting](http://www.sitepoint.com/back-to-basics-javascript-hoisting/)


問題四：How `this` work in JavaScript
-------------------------------------
寫出以下程式碼執行結果，並說明原因。

```js
var fullname = 'John Doe';
var obj = {
  fullname: 'Colin Ihrig',
  prop: {
    fullname: 'Aurelio De Rosa',
    getFullname: function() {
       return this.fullname;
    }
  }
};
 
console.log(obj.prop.getFullname());
 
var test = obj.prop.getFullname;
 
console.log(test());

```
第一個 ~`Colin Ihrig`~ `Aurelio De Rosa`

第二個 `John Doe`

JavaScript 很不一樣的一點就是 `this` 並**不是跟著 instance 走**。而是**依據誰呼叫 (invoke)** 來決定 `this` 的參考值。(這也常讓不熟稔 JS 的人嘗盡苦頭)...

第一個 `Colin Ihrig` 是因為 `getFullname()` 呼叫是由 `obj.prop` 發出，此時 `this.fullname` 即為 `obj.fullname`；而第二個 `John Doe` 則是由 `window` 呼叫 `test` 實際上是 `obj.prop.getFullname` 因此 `this.fullname === window.fullname` 於是便印出 `John Doe`。


問題五：`call()` and `apply()`
------------------------------
修正上面題目，要把第二個印出的答案變為 `Colin Ihrig` 該如何做？

2016.03.07 訂正:
```js
console.log(test.call(obj));
```

錯誤答案:
```js
console.log(test.call(obj.prop));
```

基本上 `call()` 與 `apply()` 用法差異如下：
```js
function.call(thisArg[, argument1[, argument2[, ...]]]);
function.apply(thisArg[, argumentArray]);
```

> 所有語法大致上與 apply() 相同，他們基本上不同處只有 call() 接受一連串的參數，而 apply() 單一的array作為參數 --- [MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/call)


以上簡單分享，自己也重新複習一遍了...Q_Q


ps. 原文的下面還有一些回覆，並且提供另一些題目。我們改天再一起來研究看看！

- http://tomhicks.github.io/code/2014/09/12/javascript-interview-questions.html

