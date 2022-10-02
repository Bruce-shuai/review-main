## 声明空间
在 `TypeScript` 里存在两种声明空间：**类型声明空间**与**变量声明空间**

## 类型声明空间 
类型声明空间包含用来当做**类型注解**的内容，例如下面的类型声明：
```ts
class Foo {}
interface Bar {}
type Bas = {};

let foo: Foo;
let bar: Bar;
let bas: Bas;
```

**注意，尽管你定义了 interface Bar，却并不能够把它作为一个变量来使用，因为它没有定义在变量声明空间中。** 
```ts
interface Bar {}
const bar = Bar; // Error: "cannot find name 'Bar'"
```

通过 `declare` 关键字，我们声明了一个仅在类型空间存在的变量，它在运行时完全不存在，这样就避免了略显繁琐的属性声明
```ts
// 如果只是想进行简单的类型比较，而不是变量比较
interface Foo {
  name: string;
  age: number;
} 
interface Bar {
  name: string, 
  job: string,
}
declare let foo: Foo;
declare let bar: Bar;

foo = bar;
```


## 变量声明空间 
变量声明空间包含可用作变量的内容
在上文中 `Class Foo` 提供了一个类型 `Foo` 到类型声明空间，此外它同样提供了一个变量 `Foo` 到变量声明空间

```ts
class Foo {}
const someVar = Foo;
const someOtherVar = 123;
```

与此相似，一些用 var 声明的变量，也只能在变量声明空间使用，不能用作**类型注解** (类型注解这个词就很棒)
```ts
const foo = 123;
let bar: foo; // ERROR: "cannot find name 'foo'"
```

## 模块  
* 使用ES 模块语法 

* 使用 `export`关键字导出一个变量或**类型**
```ts
export const someVar = 123; 
export type someType = {
  foo: string;
} 
```
或者 
```ts
const someVar = 123;
type someType = {
  type: string;
} 

export { someVar, someType }
```
* 使用 `import` 来导入变量或**类型**
```ts
import { someVar, someType } from './xxx'; 
```

导出所有内容 
```ts 
import * from './xxx';
import * as foo from './foo';
// 你可以使用 `foo.someVar` 和 `foo.someType` 以及其他任何从 `foo` 导出的变量或者类型
```

* 只导入模块 
```ts
import 'core-js'; // 一个普通的 polyfill 库
```

* 从其他模块导入后整体导出 
```ts
export * from './xxx'; 
```

* 从其他模块导入后，部分导出 
```ts
export { someVar } from './foo';

export { someVar as aDifferentName } from './foo';//取别名
```

* 默认导出 

```ts 
// some var
export default (someVar = 123);

// some function
export default function someFunction() {}

// some class
export default class someClass {}
```

* 默认导入 
```ts
import someLocalNameForThisFile from './foo';
```


## 动态查找
当导入路径不是相对路径时，模块解析将会模仿 Node 模块解析策略，下面我将给出一个简单例子：

当你使用 `import * as foo from 'foo'`，将会按如下顺序查找模块：
* ./node_modules/foo
* ../node_modules/foo
* ../../node_modules/foo
* 直到系统的根目录

当你使用 `import * as foo from 'something/foo'`，将会按照如下顺序查找内容
* ./node_modules/something/foo
* ../node_modules/something/foo
* ../../node_modules/something/foo
* 直到系统的根目录


## 

