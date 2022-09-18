为了充分利用多核处理器的计算能力，`HTML5` 提出了 `Web Worker` 标准，允许 `JavaScript` 脚本创建多个**子线程**，但是子线程完全受控于主线程，且不得操作 `DOM`。因此，新标准并没有改变 `JavaScript` 单线程的本质。


对应文章：
[Web Worker](https://juejin.cn/post/7131738631424704543)