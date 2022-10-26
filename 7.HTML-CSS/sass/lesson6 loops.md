```scss
$colors: (
  "primary": $primary,
  "secondary": $secondary,
  "error": $error,
  "info": $info,
  "blue": #1919e6,
  "red": #e61919,
);

// 遍历键值对
@each $key, $val in $colors {
  // 获取键值对
  .text-#{$key} {
    color: $val;
  }
  .bg-#{$key} {
    background-color: $val;
  }
}
```

```scss
$colors: (
  "blue": #1919e6,
  "red": #e61919,
);

@each $key, $val in $colors {
  // 获取键值对
  .text-#{$key} {
    color: $val;
  }
  .bg-#{$key} {
    background-color: $val;
  }

  @for $i from 1 through 9 {
    .text-#{$key}-light-#{$i} {
      color: mix(white, $val, $i * 10);
    }
    .bg-#{$key}-light-#{$i} {
      background-color: mix(white, $val, $i * 10);
    }
  }
}
```

mix 表示，white 到 $val 元素过渡，每次过渡 10%

编译结果：

```css
.text-blue {
  color: #1919e6;
}

.bg-blue {
  background-color: #1919e6;
}

.text-blue-light-1 {
  color: #3030e9;
}

.bg-blue-light-1 {
  background-color: #3030e9;
}

.text-blue-light-2 {
  color: #4747eb;
}

.bg-blue-light-2 {
  background-color: #4747eb;
}

.text-blue-light-3 {
  color: #5e5eee;
}

.bg-blue-light-3 {
  background-color: #5e5eee;
}

.text-blue-light-4 {
  color: #7575f0;
}

.bg-blue-light-4 {
  background-color: #7575f0;
}

.text-blue-light-5 {
  color: #8c8cf3;
}

.bg-blue-light-5 {
  background-color: #8c8cf3;
}

.text-blue-light-6 {
  color: #a3a3f5;
}

.bg-blue-light-6 {
  background-color: #a3a3f5;
}

.text-blue-light-7 {
  color: #babaf8;
}

.bg-blue-light-7 {
  background-color: #babaf8;
}

.text-blue-light-8 {
  color: #d1d1fa;
}

.bg-blue-light-8 {
  background-color: #d1d1fa;
}

.text-blue-light-9 {
  color: #e8e8fd;
}

.bg-blue-light-9 {
  background-color: #e8e8fd;
}

.text-red {
  color: #e61919;
}

.bg-red {
  background-color: #e61919;
}

.text-red-light-1 {
  color: #e93030;
}

.bg-red-light-1 {
  background-color: #e93030;
}

.text-red-light-2 {
  color: #eb4747;
}

.bg-red-light-2 {
  background-color: #eb4747;
}

.text-red-light-3 {
  color: #ee5e5e;
}

.bg-red-light-3 {
  background-color: #ee5e5e;
}

.text-red-light-4 {
  color: #f07575;
}

.bg-red-light-4 {
  background-color: #f07575;
}

.text-red-light-5 {
  color: #f38c8c;
}

.bg-red-light-5 {
  background-color: #f38c8c;
}

.text-red-light-6 {
  color: #f5a3a3;
}

.bg-red-light-6 {
  background-color: #f5a3a3;
}

.text-red-light-7 {
  color: #f8baba;
}

.bg-red-light-7 {
  background-color: #f8baba;
}

.text-red-light-8 {
  color: #fad1d1;
}

.bg-red-light-8 {
  background-color: #fad1d1;
}

.text-red-light-9 {
  color: #fde8e8;
}

.bg-red-light-9 {
  background-color: #fde8e8;
}
```
