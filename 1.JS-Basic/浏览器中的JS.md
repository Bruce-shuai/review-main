## 执行上下文
```js
showName() // 函数showName被执行
console.log(myname) // undefined
var myname = '极客时间'
function showName() {
  console.log('函数showName被执行')
}
```
从上面两段代码的执行结果来看，我们可以得出如下三个结论。
* 执行过程中，若使用了未声明的变量，那么 `JavaScript` 执行会报错。
* 在一个变量定义之前使用它，不会出错，但是该变量的值会为 `undefined`，而不是定义时的值。
* 在一个函数定义之前使用它，不会出错，且函数能正确执行。

### 变量提升 
针对 `var myname = '极客时间'`这段代码你可以把它看成是两行代码组成的。 
```js
var myname = undefined;  // 声明部分 
myname = '极客时间';      // 赋值部分 
```

函数的声明和赋值部分
```js 
function foo() {         // 声明
  console.log('foo')
}

var bar = function() {   // 赋值
  console.log('bar')
}
```


## 作用域链 

```js
function bar() {
  console.log(myName)
}
function foo() {
  var myName = '极客邦'
  bar()
}
var myName = '极客时间'
foo()   // 极客时间
```