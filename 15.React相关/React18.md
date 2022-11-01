资源：
[官网](https://reactjs.org/blog/2022/06/15/react-labs-what-we-have-been-working-on-june-2022.html)

[掘金博客](https://juejin.cn/post/7094037148088664078)

---

`React 17` 只发布了三个小的版本，并且都是一些打补丁和性能优化。并没有什么大的变化

## React 18 
React 18 已经放弃了对`ie`的支持(本身ie也退出市场了)

### 新特性

#### 新的管理root节点的方式(支持并发模式的渲染)

```js
// 17
ReactDOM.render(<App />, root);  // 这个也可以用在react 18，但是会报一些错误(不会影响代码本身，可以无视)

// 18  
ReactDOM.createRoot(root).render(<App />);  // 有链式效果，似乎有先后顺序的感觉。这样的方式就是开启并发模式
```

#### 针对ts，如果需要获取子组件children，需要在定义props时，显示定义
```ts
// React 17
interface MyButtonProps {
  color: string;
}

const MyButton: React.FC<MyButtonProps> = ({ children }) => {
  // 在 React 17 的 FC 中，默认携带了 children 属性
  return <div>{children}</div>;
};

export default MyButton;

// React 18
interface MyButtonProps {
  color: string;
  children?: React.ReactNode;
}

const MyButton: React.FC<MyButtonProps> = ({ children }) => {
  // 在 React 18 的 FC 中，不存在 children 属性，需要手动申明
  return <div>{children}</div>;
};

export default MyButton;
```

#### setState 批处理
所谓**批处理**即 在数据层，将多个状态更新批量处理，合并成一次更新（在视图层，将多个渲染合并成一次渲染）

在`React 18以前`，只会在`React事件处理函数`(例如`onClick`)中进行批处理，而在promise、setTimeout、原生事件中不会进行批处理。

```js
import React, { useState } from 'react';

// React 18 之前
const App: React.FC = () => {
  console.log('App组件渲染了！');
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);
  return (
    <button
      onClick={() => {
        setCount1(count => count + 1);
        setCount2(count => count + 1);
        // 在React事件中被批处理
      }}
    >
      {`count1 is ${count1}, count2 is ${count2}`}
    </button>
  );
};

export default App;
```

```js
import React, { useState } from 'react';

// React 18 之前
const App: React.FC = () => {
  console.log('App组件渲染了！');
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);
  return (
    <div
      onClick={() => {
        setTimeout(() => {
          setCount1(count => count + 1);
          setCount2(count => count + 1);
        });
        // 在 setTimeout 中不会进行批处理
      }}
    >
      <div>count1： {count1}</div>
      <div>count2： {count2}</div>
    </div>
  );
};

export default App;
```

```js
import React, { useEffect, useState } from 'react';

// React 18 之前
const App: React.FC = () => {
  console.log('App组件渲染了！');
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);
  useEffect(() => {
    document.body.addEventListener('click', () => {
      setCount1(count => count + 1);
      setCount2(count => count + 1);
    });
    // 在原生js事件中不会进行批处理
  }, []);
  return (
    <>
      <div>count1： {count1}</div>
      <div>count2： {count2}</div>
    </>
  );
};

export default App;
```

#### 使用`flushSync`, 可以退出批处理  
批处理是一个破坏性改动，如果你想退出批量更新，你可以使用 `flushSync`：
```js
import React, { useState } from 'react';
import { flushSync } from 'react-dom';

const App: React.FC = () => {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);
  return (
    <div
      onClick={() => {
        flushSync(() => {
          setCount1(count => count + 1);
        });
        // 第一次更新
        flushSync(() => {
          setCount2(count => count + 1);
        });
        // 第二次更新
      }}
    >
      <div>count1： {count1}</div>
      <div>count2： {count2}</div>
    </div>
  );
};

export default App;
```

#### 严格模式
当你使用严格模式时，`React` 会对每个组件进行**两次渲染**，以便你观察一些意想不到的结果。在 `React 17` 中，取消了其中一次渲染的控制台日志，以便让日志更容易阅读。
为了解决社区对这个问题的困惑，在 `React 18` 中，官方取消了这个限制。如果你安装了`React DevTools`，第二次渲染的日志信息将显示为灰色，以柔和的方式显式在控制台。

#### 新的API

**一、useId**
支持同一个组件在客户端和服务端生成相同的唯一的 `ID`, 在`React SSR`渲染中，有比较大的作用

```js
const id = useId();
```



#### 并发模式
