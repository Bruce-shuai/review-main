## 类型查询操作符：typeof

`TypeScript` 存在两种功能不同的 `typeof` 操作符。我们最常见的一种 `typeof` 操作符就是 `JavaScript` 中，用于检查变量类型的 `typeof` ，它会返回 `"string" / "number" / "object" / "undefined"` 等值。而除此以外， `TypeScript` 还新增了用于类型查询的 `typeof` ，即 `Type Query Operator`，这个 `typeof` 返回的是一个 `TypeScript` 类型：

```ts
const str = "linbudu";

const obj = { name: "linbudu" };

const nullVar = null;
const undefinedVar = undefined;

const func = (input: string) => {
  return input.length > 10;
};

type Str = typeof str; // "linbudu"
type Obj = typeof obj; // { name: string; }
type Null = typeof nullVar; // null
type Undefined = typeof undefined; // undefined
type Func = typeof func; // (input: string) => boolean
```

```ts
const func = (input: string) => {
  return input.length > 10;
};

const func2: typeof func = (name: string) => {
  return name === "linbudu";
};
```

你不仅可以直接在**类型标注**中使用 typeof，还能在工具类型中使用 typeof。

```ts
const func = (input: string) => {
  return input.length > 10;
};

const func2: typeof func = (name: string) => {
  return name === "linbudu";
};
```

绝大部分情况下，`typeof`返回的类型就是当你把鼠标悬浮在变量名上时出现的推导后的类型，并且是最窄的推导程度(即到字面量类型的级别)。你也不必担心混用了这两种 `typeof`，在逻辑代码中使用的 `typeof` 一定会是 `JavaScript` 中的 `typeof`，而类型代码（如类型标注、类型别名中等）中的一定是类型查询的 `typeof` 。同时，为了更好地避免这种情况，也就是隔离类型层和逻辑层，类型查询操作符后是**不允许使用表达式**的：


## 类型守卫
```ts
declare const strOrNumOrBool: string | number | boolean;

if (typeof strOrNumOrBool === "string") {
  // 一定是字符串！
  strOrNumOrBool.charAt(1);
} else if (typeof strOrNumOrBool === "number") {
  // 一定是数字！
  strOrNumOrBool.toFixed();
} else if (typeof strOrNumOrBool === "boolean") {
  // 一定是布尔值！
  strOrNumOrBool === true;
} else {
  // 要是走到这里就说明有问题！
  const _exhaustiveCheck: never = strOrNumOrBool;
  throw new Error(`Unknown input type: ${_exhaustiveCheck}`);
}

```
在这里，我们实际上通过 if 条件中的表达式进行了类型保护，即告知了流过这里的分析程序每个 if 语句代码块中变量会是何类型。这即是编程语言的类型能力中最重要的一部分：与实际逻辑紧密关联的类型。我们从逻辑中进行类型地推导，再反过来让类型为逻辑保驾护航。

```ts
function isString(input: unknown): boolean {
  return typeof input === "string";
}

function foo(input: string | number) {
  if (isString(input)) {
    // 类型“string | number”上不存在属性“replace”。
    (input).replace("linbudu", "linbudu599")
  }
  if (typeof input === 'number') { }
  // ...
}
```
奇怪的事情发生了，我们只是把逻辑提取到了外面而已，如果 isString 返回了 true，那 input 肯定也是 string 类型啊？

想象类型控制流分析这条河流，刚流进 if (isString(input)) 就戛然而止了。因为 isString 这个函数在另外一个地方，内部的判断逻辑并不在函数 foo 中。这里的类型控制流分析做不到跨函数上下文来进行类型的信息收集（但别的类型语言中可能是支持的）。

实际上，将判断逻辑封装起来提取到函数外部进行复用非常常见。为了解决这一类型控制流分析的能力不足， TypeScript 引入了 is 关键字来显式地提供类型信息：
```ts
function isString(input: unknown): input is string {
  return typeof input === "string";
}

function foo(input: string | number) {
  if (isString(input)) {
    // 正确了
    (input).replace("linbudu", "linbudu599")
  }
  if (typeof input === 'number') { }
  // ...
}

```

`isString` 函数称为**类型守卫**，在它的返回值中，我们不再使用 `boolean` 作为类型标注，而是使用 `input is string` 这么个奇怪的搭配，拆开来看它是这样的：
* input 函数的某个参数；
* `is string`，即 `is` 关键字 + 预期类型，即如果这个函数成功返回为 true，那么 is 关键字前这个入参的类型，就会被这个类型守卫调用方后续的类型控制流分析收集到。
