如果想要开发一个网站的信息提示按钮：

常规做法如下：

```html
<div class="container">
  <button>Bell Icon</button>
  <span class="badge">5</span>
</div>
```

```css
.container {
  position: relative;
}
.badge {
  position: absolute;
  top: -0.75em;
  right: -0.75em;
  width: 1.5em;
  height: 1.5em;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  background-color: red;
  color: white;
}
```

但上述这种方式是笨重的。下述有种简单的方法，只需要使用到`attr()`来显示文案内容即可。 即 `attr`, `content`, `data attributes` 三者配合可以有丰富的内容。

```html
<button data-notification-count>Bell Icon</button>
```

```css
[data-notification-count] {
  position: relative;
}
[data-notification-count]::before {
  content: attr(data-notification-count);
  position: absolute;
  top: -0.75em;
  right: -0.75em;
  width: 1.5em;
  height: 1.5em;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  background-color: red;
  color: white;
}
```
