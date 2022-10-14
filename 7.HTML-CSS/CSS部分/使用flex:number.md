可以使用 `flex：number` 来响应式分配宽度
例如：

```html
<div class="box-container">
  <div class="box2"></div>
  <div class="box1"></div>
</div>
```

```css
.box-container {
  display: flex;
}

.box1 {
  flex: 2;
}

.box2 {
  flex: 3;
}

/* 那么box1 和 box2就是以2:3的比例来进行分配box-container */
```
