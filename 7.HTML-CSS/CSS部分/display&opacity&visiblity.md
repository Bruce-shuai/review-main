## display:none vs opacity:0 vs visibility:hidden

### 三者不同之处

- `display: none`: 不会占 DOM 节点的位置。而其他两个是会占位的

- `display:none` 和 `visibility: hidden`不会让浏览器响应任何事件。 `visibility: hidden`有些类似`opacity: 0` + `pointer-event: none`。

- 关于可访问性，`opacity: 0` 是使元素按 Tab 键顺序可访问的唯一属性，并且屏幕阅读器可以读取元素的内容。

- 应用`display: none`或`opacity: 0`将影响子元素。`visibility: hidden`，另一方面，不会改变任何孩子的可见性。

值得注意的是，如果要测量元素的大小，则根本无法使用`display: none`。

正如在第一个区别中提到的，带有的元素`display: none`不会占用页面上的任何空间。因此，所有与元素大小相关的属性，例如`clientHeight`、`clientWidth`、`height`、`offsetHeight`、`offsetWidth`、`scrollHeight`和都为零。`scrollWidthwidth`,`getBoundingClientRect()`该方法返回的所有属性也为零。

同样，带有 `visibility: hidden` 的元素将具有空的内部文本（与 innerText 属性等效）。
