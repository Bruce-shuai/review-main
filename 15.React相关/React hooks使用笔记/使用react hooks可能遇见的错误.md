视频：https://www.youtube.com/watch?v=GGo3MVBFr1A

**下面的所有例子**：https://codesandbox.io/s/gifted-artem-odnir7?file=/src/Demo1/After1.jsx

## 在表格数据的时候，用 useRef 来代替 useState

因为使用 `useState` 来定义变量，那么在输入框每输入一次内容都会造成一次重渲染。而使用 `useRef` 则可以完美避免这一件事。

## 尽量在 useState 里的 setState 中使用回调

如果不在 `setState` 中使用回调会出现俩比较明显的问题。
问题一：由于 `react` 批处理的原因，下面的多个 `setCount` 操作会变为最后一次的操作
问题二：如果是在异步的环境下，进行点击。我再点击同步的按钮，那么异步的值会和同步的值起冲突

## 避免在 useEffect 中使用引用类型做依赖，不然会造成许多没必要的渲染甚至死循环

可以使用 `useMemo` 来解决问题

```js
const person = useMemo(() => {
  return { age, name };
}, [age, name]);

useEffect(() => {
  console.log(person);
}, [person]);
```

## 对于快速点击的请求，终止前面的请求，以确保点击的请求获取的数据是最新的内容

```jsx
import { useEffect, useState } from "react";

export function useFetch(url) {
  const [loading, setLoading] = useState(true);
  const [data, setData] = useState();
  const [error, setError] = useState();

  // 使用 AbortController 来终止上一次的实例，这是fetch版本
  useEffect(() => {
    const controller = new AbortController();
    setLoading(true);
    fetch(url, { signal: controller.signal })
      .then(setData)
      .catch((err) => {
        if (err.name === "AbortError") {
          console.log("cancelled!");
        } else {
          // todo: handle err
        }
      })
      .finally(() => setLoading(false));

    // 终止上一次的请求数据
    return () => {
      controller.abort();
    };
  }, [url]);

  // axios 版本
  useEffect(() => {
    const cancelToken = axios.CancelToken.source();
    setLoading(true);
    axios(url, { cancelToken: cancelToken.token })
      .then(setData)
      .catch((err) => {
        if (axios.isCancel(err)) {
        } else {
          // todo: handle err
        }
      })
      .finally(() => setLoading(false));

    // 终止上一次的请求数据
    return () => {
      cancelToken.cancel();
    };
  }, [url]);
}
```

## 不要在 state 被修改的函数附近获取最新的 state，而是应该使用 useEffect 来进行获取最新数据

```jsx
function Counter() {
  function add() {
    setState(count => count + 1);  // count = 2, count + 1 = 3
    console.log(count); // 2，只能获取到当前setState中回调里的参数state，因为回调并未被执行(非同步)，所以count不能获取到对应的最新值
}

useEffect(() => {
  console.log(count);  // 3, 因为是在dom渲染完之后，依赖项的count已经是最新值了，所以会执行useEffect的回调，并返回最新的count
}, [count])
```
