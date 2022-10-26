```scss
// color palette
$colors: (
  "primary": $primary,
  "secondary": $secondary,
  "error": $error,
  "info": $info,
  "blue": #1919e6,
  "red": #e61919,
);

@debug map-get($colors, "red"); // #e61919
@debug map-has-key($colors, "secondary"); // true
@debug map-remove($colors, "secondary"); // 移除secondary
@debug map-merge(
  $colors,
  (
    "pink": #ffc0cb,
  )
); // 添加pink
```
