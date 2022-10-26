```scss
@use "sass:math";
.card {
  display: block;
  padding: $base-padding;
  border: $base-border-thickness solid #ddd;
  box-shadow: $base-box-shadow;
  border-radius: math.div($base-border-radius, 4); // div 是除法
}
```

除法：math.div

可以通过 `@debug` 来打 scss 断点

```scss
@debug "hello";
@debug math.div(10, 3);
@debug math.floor(2.6);
@debug math.max(1px, 20px, 15px);
```
