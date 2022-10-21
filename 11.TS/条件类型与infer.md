### 条件类型基础

条件类型的语法类似于我们平时常用的三元表达式，它的基本语法如下（伪代码）：

```ts
ValueA === Value B ? Result1 : Result2;
TypeA extends TypeB ? Result1 : Result2;
```

但需要注意的是，条件类型中使用 extends 判断类型的兼容性，而非判断类型的全等性。这是因为在类型层面中，对于能够进行赋值操作的两个变量，我们并不需要它们的类型完全相等，只需要具有兼容性，而两个完全相同的类型，其 extends 自然也是成立的。

条件类型绝大部分场景下会和泛型一起使用，我们知道，泛型参数的实际类型会在实际调用时才被填充（类型别名中显式传入，或者函数中隐式提取），而条件类型在这一基础上，可以基于填充后的泛型参数做进一步的类型操作，比如这个例子：

```ts
type LiteralType<T> = T extends string ? "string" : "other";

type Res1 = LiteralType<"heYang">; // "string"
type Res2 = LiteralType<599>; // "other"
```

```ts
export type LiteralType<T> = T extends string
  ? "string"
  : T extends number
  ? "number"
  : T extends null
  ? "null"
  : T extends undefined
  ? "undefined"
  : never;

type Res1 = LiteralType<"linbudu">; // "string"
type Res2 = LiteralType<599>; // "number"
type Res3 = LiteralType<true>; // "boolean"
```

### infer 关键字

TS 中支持通过 `infer` 关键字在**条件类型**中提取类型的某一部分信息。

```ts
type FunctionReturnType<T extends Func> = T extends (...args: any[]) => infer R
  ? R
  : never;
```

`infer` 是 `inference` 的缩写，意为推断，如 `infer R` 中 `R` 就表示 待推断的类型。 **`infer` 只能在条件类型中使用**，因为我们实际上仍然需要类型结构是一致的，比如上例中类型信息需要是一个函数类型结构，我们才能提取出它的返回值类型。如果连函数类型都不是，那我只会给你一个 `never` 。

```ts
type Swap<T extends any[]> = T extends [infer A, infer B] ? [B, A] : T;

type SwapResult1 = Swap<[1, 2]>; // 符合元组结构，首尾元素替换[2, 1]
type SwapResult2 = Swap<[1, 2, 3]>; // 不符合结构，没有发生替换，仍是 [1, 2, 3]
```

```ts
// 提取首位两个
type ExtractStartAndEnd<T extends any[]> = T extends [
  infer Start,
  ...any[],
  infer End
]
  ? [Start, End]
  : T;

// 调换首尾两个
type SwapStartAndEnd<T extends any[]> = T extends [
  infer Start,
  ...infer Left,
  infer End
]··
  ? [End, ...Left, Start]
  : T;

// 调换开头两个
type SwapFirstTwo<T extends any[]> = T extends [
  infer Start1,
  infer Start2,
  ...infer Left
]
  ? [Start2, Start1, ...Left]
  : T;
```

是的，`infer` 甚至可以和 `rest` 操作符一样同时提取一组不定长的类型，而 `...any[]` 的用法是否也让你直呼神奇？上面的输入输出仍然都是数组，而实际上我们完全可以进行结构层面的转换。比如从数组到联合类型：

```ts
type ArrayItemType<T> = T extends Array<infer ElementType>
  ? ElementType
  : never;

type ArrayItemTypeResult1 = ArrayItemType<[]>; // never
type ArrayItemTypeResult2 = ArrayItemType<string[]>; // string
type ArrayItemTypeResult3 = ArrayItemType<[string, number]>; // string | number
```

```ts
// 提取对象的属性类型
type PropType<T, K extends keyof T> = T extends { [Key in K]: infer R }
  ? R
  : never;
```

#### 分布式条件类型

分布式条件类型（Distributive Conditional Type），也称条件类型的分布式特性，只不过是条件类型在满足一定情况下会执行的逻辑而已。

```ts
type Condition<T> = T extends 1 | 2 | 3 ? T : never;
// 1 | 2 | 3
type Res1 = Condition<1 | 2 | 3 | 4 | 5>; // 泛型传入联合类型，则每个元素都会依次进行判断一下

// never  因为 1 | 2 | 3 | 4 | 5 不是 1 | 2 | 3 的子元素而是它的父元素
type Res2 = 1 | 2 | 3 | 4 | 5 extends 1 | 2 | 3 ? 1 | 2 | 3 | 4 | 5 : never;
```
