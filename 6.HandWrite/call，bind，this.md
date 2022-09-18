```js
Function.prototype.myCall = function(obj, ...args) {
  // 先判断是否传入了值
  obj = obj || window   // 考虑兼容性问题
  let fn = Symbol();    // 创建一个独一无二的属性
  obj[fn] = this;       // this 代指的是调用的函数
  let result = obj[fn](...args);   // 要返回这个值的
  delete obj[fn];
  return result;
}

// apply ---> 代码逻辑和call非常相似(只不过这里借助的是arguments)
Function.prototype.myApply = function(obj) {  // 可以写成 obj, ...args吗
  obj = obj || window;
  let fn = Symbol();
  obj[fn] = this;
  let result; 
  if (arguments[1]) {  // 如果传入了参数,参数是数组
    result = obj[fn](...arguments[1]);
  } else {
    result = obj[fn]();
  }
  delete obj[fn];
  return result;
}


// bind  最后返回的是一个函数

// bind和apply的区别在于,bind是返回一个绑定好的函数,apply是直接调用.
// 其实想一想实现也很简单,就是返回一个函数,里面执行了apply上述的操作而已.
// 不过有一个需要判断的点,因为返回新的函数,要考虑到使用new去调用,并且new的优先级比较高,所以需要判断new的调用,
// 还有一个特点就是bind调用的时候可以传参,调用之后生成的新的函数也可以传参,效果是一样的,所以这一块也要做处理

Function.prototype.myBind = function(obj) {
  let that = this;
  let args = [...arguments].slice(1);  // 将类数组转化为数组
  // 返回一个函数
  return function func() {
    // 因为返回了一个函数，我们可以new F(), 所以需要判断
    if(this instanceof func) {  // 当作为构造函数时，this 指向实例，此时 this instanceof func 结果为 true
      return new that(...args, ...arguments);   // 
    }
    // that 是为了锁定住最早调用bind的方法是谁
    return that.apply(obj, [...args, ...arguments]);
  }
}
```

