```js
function sleep(timer) {
  return new Promise((resolve) => {
    setTimeout(() => resolve('结束'), timer)
  }).then(res => console.log(res))
}
```
