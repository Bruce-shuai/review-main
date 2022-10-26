```scss
/** variables.scss */
h1 {
  color: #326dee;
  margin-bottom: 0.75rem;
}

a {
  color: #1ac888;
}

button {
  color: white;
  border: 0;
  background: #326dee;
  border-radius: 20px;
  padding: 0.75rem;
}

.error {
  color: #d32752;
  border-color: #d32752;
  border-style: solid;
  margin: 0.75rem;
  padding: 0.75rem;
  border-radius: 20px;
  border-width: 1px;
}

.notification {
  color: #1ac888;
  border-color: #1ac888;
  border-style: solid;
  margin: 0.75rem;
  padding: 0.75rem;
  border-radius: 20px;
  border-width: 1px;
}

/** index.scss */
@import "variables.scss";

h1 {
  color: $primary;
  margin-bottom: $base-margin;
}
a {
  color: $secondary;
}
button {
  color: white;
  border: 0;
  background: $primary;
  border-radius: $base-border-radius;
  padding: $base-padding;
}
.error {
  color: $error;
  border-color: $error;
  border-style: solid;
  margin: $base-margin;
  padding: $base-padding;
  border-radius: $base-border-radius;
  border-width: $base-border-thickness;
}
.notification {
  color: $secondary;
  border-color: $secondary;
  border-style: solid;
  margin: $base-margin;
  padding: $base-padding;
  border-radius: $base-border-radius;
  border-width: $base-border-thickness;
}
```

注意，这个 css 模块导入是有顺序的
