### 手写Promise

```js
class MyPromise {
  // 三种状态
  static PENDING = '待定';
  static FULFILLED = '成功';
  static REJECTED = '失败';

  constructor(executor) {
    this.status = MyPromise.PENDING;  // 默认为pending
    this.result = null;
    this.resolveCallbacks = [];
    this.rejectCallbacks = [];

    try {
      executor(this.resolve.bind(this), this.reject.bind(this));
    } catch(e) {
      this.reject(e);
    }
  }

  resolve(result) {
    // resolve, reject 是要在当前作用域下最后执行的，所以用setTimeout来实现
    setTimeout(() => {
      if (this.status === MyPromise.PENDING) {
        this.status = MyPromise.FULFILLED;
        this.result = result;
        // TODO: 这一步的重要性是啥？ 不是在then中的状态判断后就会执行onFULFILLED吗？
        this.resolveCallbacks.forEach(cb => cb(result));
      }
    })
  }

  reject(result) {
    setTimeout(() => {
      if (this.status === MyPromise.PENDING) {
        this.status = MyPromise.REJECTED;
        this.result = result;
        this.rejectCallbacks.forEach(cb => cb(result))
      }
    })
  }

  // then 是一定会返回一个新的promise对象
  then(onFULFILLED, onREJECTED) {
    return new MyPromise((resolve, reject) => {
      onFULFILLED = typeof onFULFILLED 'function' ? onFULFILLED : () => {}
      onREJECTED = typeof onREJECTED === 'function' ? onREJECTED : () => {}
      // 只能执行两个参数中的其中一个
      if (this.status === MyPromise.PENDING) {
        // 让then里的回调稍后执行，让resolve执行后再执行then里面的内容
        // TODO: 有个疑问，为啥这里是一个队列？
        this.resolveCallbacks.push(onFULFILLED)
        this.rejectCallbacks.push(onREJECTED)
      }
      if (this.status === MyPromise.FULFILLED) {
        // 设置异步执行
        setTimeout(() => {
          onFULFILLED(this.result)
        })
      } 
      if (this.status === MyPromise.REJECTED) {
        setTimeout(() => {
          onREJECTED(this.result)
        })
      }
    })
  }
}
```

### Promise.all

### Promise.race
