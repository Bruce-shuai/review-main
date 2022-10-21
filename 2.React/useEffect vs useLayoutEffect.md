### useEffect vs useLayoutEffect

二者区别度很小，唯一区别在于，`useEffect` 的触发阶段是在 `DOM` 渲染完之后触发。而 `useLayoutEffect` 是在 `DOM` 改变后，渲染之前进行触发。
所以，如果我们的效果会改变 `DOM`（比如获取元素的滚动位置或其他样式）或涉及动画，则更喜欢 `useLayoutEffect` 而不是 `useEffect`。其他情况，基本都是使用的 `useEffect` 来进行开发。
