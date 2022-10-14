## any

一个被标记为`any`类型的参数可以接受任意类型的值。也可以被赋值给其他任意类型的变量。
要想正常使用`any`类型，需要在`tsconfig`中关闭 `noImplicitAny`

**过多使用 any 是有害的**
any 类型的主要意义，其实就是为了表示一个无拘无束的“任意类型”，它能兼容所有类型，也能够被所有类型兼容。这一作用其实也意味着类型世界给你开了一个外挂，无论什么时候，你都可以使用 any 类型跳过类型检查。当然，运行时出了问题就需要你自己负责了。 `any` 放弃了类型检查

## unknown

如果你只是想表达一个未知类型，更合理的方式是使用 `unknown`。
注意：unknown 可以被任意类型赋值(这部分和 any 一样)，不过不能赋值给其他类型

```ts
let unknownVar: unknown = "linbudu";

unknownVar = false;
unknownVar = "linbudu";
unknownVar = {
  site: "juejin",
};

unknownVar = () => {};

const val1: string = unknownVar; // Error
const val2: number = unknownVar; // Error
const val3: () => {} = unknownVar; // Error
const val4: {} = unknownVar; // Error

const val5: any = unknownVar;
const val6: unknown = unknownVar;
```

## never

never 就是这么一个“什么都没有”的类型。相比于 void ，never 还要更加空白一些。

```ts
type UnionWithNever = "linbudu" | 599 | true | void | never; // never直接被无视掉
```

在编程语言的类型系统中，`never` 类型被称为 `Bottom Type`，是整个类型系统层级中最底层的类型。和 `null`、`undefined` 一样，它是所有类型的子类型，但只有 `never` 类型的变量能够赋值给另一个 `never` 类型变量。
