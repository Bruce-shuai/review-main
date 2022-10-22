### 组件声明

```tsx
export interface IContainerProps {
  visible: boolean;
  controller: () => void;
}

const Container = (props: IContainerProps) => {
  return <p>he yang</p>;
};

const Container = ({
  visible = false,
  controller = () => {},
}: IContainerProps) => {
  return <p>he yang</p>;
};
```

这么做看起来很朴素，但 TypeScript 仍然能把返回值类型推导出来，以上函数的类型可以被正确地推导为 `() => JSX.Element`。
但这样只能说明它是一个函数，并不能从类型层面上标明它是一个 React 组件，也无法约束它必须返回一个合法的组件。我们可以加上显式的类型标注来确保它返回一个有效组件：

```tsx
// JSX 是一个命名空间，来自于 @types/react
const Container = (): JSX.Element => {
  return <p>he yang</p>;
};
```

除了这种方式，React 中还提供了 FC 这一类型来支持更精确的类型声明：

```tsx
import React from "react";

export interface IContainerProps {
  visible: boolean;
  controller: () => void;
}

const Container: React.FC<IContainerProps> = ({
  visible = false,
  controller = () => {},
}: IContainerProps) => {
  return <p>he yang</p>;
};
```

来看看 FC 的声明是什么样的：

```tsx
interface FunctionComponent<P = {}> {
  (props: PropsWithChildren<P>, context?: any): ReactElement<any, any> | null;
  propTypes?: WeakValidationMap<P> | undefined;
  contextTypes?: ValidationMap<any> | undefined;
  defaultProps?: Partial<P> | undefined;
  displayName?: string | undefined;
}
```

可以看到，代表着属性类型的泛型参数 P 实际上就是直接传给了类型别名 PropsWithChildren ，而它其实就是为 Props 新增了一个 children 属性：

```tsx
type PropsWithChildren<P> = P & { children?: ReactNode | undefined };
```

也就是说我们连这个组件的 `children` 都约束了：

```tsx
const App = () => {
  return (
    <Container visible controller={() => {}}>
      <p>TypeScript + React!</p>
    </Container>
  );
};
```

### 组件泛型

使用简单函数和使用 FC 的重要差异之一就在于，使用 FC 时你无法再使用组件泛型。组件泛型即指，为你的组件属性再次添加一个泛型，例如：

```tsx
import { PropsWithChildren } from "react";

interface ICellProps<TData> {
  field: keyof TData;
}

const Cell = <T extends Record<string, any>>(
  props: PropsWithChildren<ICellProps<T>>
) => {
  return <p></p>;
};

interface IDataStruct {
  name: string;
  age: number;
}

const App = () => {
  return (
    <>
      <Cell<IDataStruct> field="name"></Cell>
      <Cell<IDataStruct> field="age"></Cell>
    </>
  );
};
```

### 泛型坑位

**useState**

```tsx
// 推导为 string 类型
const [state1, setState1] = useState("linbudu");
// 此时类型为 string | undefined
const [state2, setState2] = useState<string>();
```

需要注意的是在显式传入泛型时，如果像上面的例子一样没有提供初始值，那么 state2 的类型实际上会是 string | undefined，这是因为在 useState 声明中对是否提供初始值的两种情况做了区分重载：

```tsx
// 提供了默认值
function useState<S>(
  initialState: S | (() => S)
): [S, Dispatch<SetStateAction<S>>];
// 没有提供默认值
function useState<S = undefined>(): [
  S | undefined,
  Dispatch<SetStateAction<S | undefined>>
];
```

另外一个常见的场景是对于在初始阶段是一个空对象的状态，你可能会使用断言来这么做：

```tsx
const [data, setData] = useState<IData>({} as IData);
```

这么做的坏处在于，后续的调用方中会认为这是一个完整实现了 IData 结构的对象，可能会出现遗漏的未赋值属性，此时你也可以使用 Partial 类型标记它为可选：

```tsx
const [data, setData] = useState<Partial<IData>>({});
```

如果你需要消费 useState 返回值的类型，可以搭配 ReturnType：

```tsx
// 相当于 useState(0) 的返回值类型
type State = ReturnType<typeof useState<number>>;
```

**useCallback & useMemo**
隐式推导与显示提供

```tsx
const Container = () => {
  // 泛型推导为 (input: number) => boolean
  const handler1 = useCallback((input: number) => {
    return input > 599;
  }, []);

  // 显式提供为 (input: number, compare: boolean) => boolean
  const handler2 = useCallback<(input: number, compare: boolean) => boolean>(
    (input: number) => {
      return input > 599;
    },
    []
  );

  // 推导为 string
  const result1 = useMemo(() => {
    return "some-expensive-process";
  }, []);

  // 显式提供
  const result2 = useMemo<{ name?: string }>(() => {
    return {};
  }, []);
};
```

通常情况下，我们不会主动为 useCallback 提供泛型参数，因为其传入的函数往往已经确定。而为 useMemo 提供泛型参数则要常见一些，因为我们可能希望通过这种方式来约束 useMemo 最后的返回值。

**useReducer**
useReducer 可以被视为更复杂一些的 useState，它们关注的都是数据的变化。不同的是 useReducer 中只能由 reducer 按照特定的 action 来修改数据，但 useState 则可以随意修改。useReducer 有三个泛型坑位，分别为 reducer 函数的类型签名、数据的结构以及初始值的计算函数，我们直接看实际使用即可：

```tsx
import { useReducer } from "react";

const initialState = { count: 0 };

type Actions =
  | {
      type: "inc";
      payload: {
        count: number;
        max?: number;
      };
    }
  | {
      type: "dec";
      payload: {
        count: number;
        min?: number;
      };
    };

function reducer(state: typeof initialState, action: Actions) {
  switch (action.type) {
    case "inc":
      return {
        count: action.payload.max
          ? Math.min(state.count + action.payload.count, action.payload.max)
          : state.count + action.payload.count,
      };
    case "dec":
      return {
        count: action.payload.min
          ? Math.max(state.count + action.payload.count, action.payload.min)
          : state.count - action.payload.count,
      };
    default:
      throw new Error("Unexpected Action Received.");
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <>
      Count: {state.count}
      <button
        onClick={() =>
          dispatch({ type: "dec", payload: { count: 599, min: 0 } })
        }
      >
        -(min: 0)
      </button>
      <button
        onClick={() =>
          dispatch({
            type: "inc",
            payload: {
              count: 599,
              max: 599,
            },
          })
        }
      >
        +(max: 599)
      </button>
    </>
  );
}
```

**useRef & useImperativeHandle**

useRef 的常见使用场景主要包括两种，存储一个 DOM 元素引用和持久化保存一个值。

```tsx
const Container = () => {
  const domRef = useRef<HTMLDivElement>(null);
  const valueRef = useRef<number>(599);
  const operateRef = () => {
    domRef.current?.getBoundingClientRect();
    valueRef.current += 1;
  };
  return (
    <div ref={domRef}>
      <p>林不渡</p>
    </div>
  );
};
```

对于 domRef，此时其类型（current）会被推断为 RefObject<HTMLDivElement>，而 valueRef 的值类型则为 MutableRefObject<number>，这是完全符合预期的。因为我们并不会去修改挂载了 DOM 引用的 ref，而确实会修改值引用的 ref ，所以后者会是 Mutable 的。

然而实际上，这一差异并不是通过判断是否被应用在了 DOM 引用来实现的（也不需要做到如此智能），从 useRef 的类型定义可以看出，对于初始值为 null 的 useRef，其类型均会被推导为 RefObject：

```tsx
function useRef<T>(initialValue: T): MutableRefObject<T>;
function useRef<T>(initialValue: T | null): RefObject<T>;
function useRef<T = undefined>(): MutableRefObject<T | undefined>;
```

HTMLDivElement 这一类型来自于 TypeScript 内置，在使用 ref 来引用 DOM 元素时，你应当使用尽可能精确的元素类型，如 HTMLInputElement、HTMLIFrameElement 等，而不是 HTMLElement 这样宽泛的定义，因为这些精确元素定义的内部封装了更加具体的类型定义，包括 HTML 属性、事件入参等。

### 内置类型定义

除了上面介绍的泛型坑位以外，在 React 中想要用好 TypeScript 的另一个关键因素就是使用 @types/react 提供的类型定义，最常见的就是事件类型，比如输入框值变化时的 ChangeEvent 和鼠标事件通用的 MouseEvent：

```tsx
import { useState } from "react";
import type { ChangeEvent, MouseEvent } from "react";

const Container = () => {
  const [v, setV] = useState("xxxx");
  const handleChange = (event: ChangeEvent<HTMLInputElement>) => {};
  const handleClick = (event: MouseEvent<HTMLButtonElement>) => {};

  return (
    <>
      <input value={v} onChange={handleChange} />
      <button onClick={handleClick}>Click me!</button>
    </>
  );
};
```

需要注意的是，ChangeEvent 和 MouseEvent 上还具有一个泛型坑位，用于指定发生此事件的元素类型，我们可以在这里进一步传入 HTMLButtonElement 这样更精确的元素类型获得更严格的类型检查。

除了使用 ChangeEvent 作为参数类型，React 还提供了整个函数的类型签名，如 ChangeEventHandler：

```tsx
const handleChange: ChangeEventHandler<HTMLInputElement> = (e) => {};
```

除了这些事件类型以外，还有一个常见的类型是在你声明组件属性中的样式时会用到的，那就是 CSSProperties ，它描述了所有的 CSS 属性及对应的属性值类型，你也可以直接用它来检查 CSS 样式时的值：

```tsx
import type { CSSProperties } from "react";

export interface IContainerProps {
  style: CSSProperties;
}

const css: CSSProperties = {
  display: "flex",
  alignContent: "center",
  justifyContent: "center",
};

const Container = ({ style }: IContainerProps) => {
  return <p style={style}>林不渡！</p>;
};
```

### JSX.Element & ReactElement & ReactNode

```tsx
type PropsWithChildren<P> = P & { children?: ReactNode | undefined };

interface FunctionComponent<P = {}> {
  (props: PropsWithChildren<P>, context?: any): ReactElement<any, any> | null;
}
```

ReactElement 是 createElement、cloneElement 等 factory 方法的返回值类型，它本质上指的是一个有效的 JSX 元素，即 JSX.Element。而 ReactNode 可以认为包含了 ReactElement ，它还包含 null、undefined 以及 ReactFragment 等一些特殊的部分，其类型定义如下：

```tsx
type ReactText = string | number;
type ReactChild = ReactElement | ReactText;
type ReactNode =
  | ReactChild
  | ReactFragment
  | ReactPortal
  | boolean
  | null
  | undefined;
```

---

### 扩展阅读

**FC 并不是完美的**

- 函数声明组件需要额外的返回值类型标注（JSX.Element）才能校验组件合法，并且可以再使用组件泛型来进一步确保类型安全。
- FC 可以简化函数的声明，但是无法使用组件泛型。

在这一部分，我们再来了解下这两者更多的差异，以及为什么说 FC 并不是完美的 （举例来说，在 Create-React-App 的最新模板代码里，已经不再使用 FC 了）。
但我们看看 FC 的类型定义：

```ts
type PropsWithChildren<P> = P & { children?: ReactNode | undefined };

interface FunctionComponent<P = {}> {
  (props: PropsWithChildren<P>, context?: any): ReactElement<any, any> | null;
}
```

你会发现 FC 的属性中是默认包含了 children 这一属性的（对应到 Vue 中则是插槽 slot 的概念），但并不是所有时候我们的组件都会包含一个 children：

```tsx
const App = () => {
  return (
    <>
      <ContainerWithoutChildren />
      <ContainerWithChildren>linbudu</ContainerWithChildren>
    </>
  );
};
```

如果你为 ContainerWithoutChildren 也传入了 children，虽然不会报错，但这个 children 实际上并不会渲染出来。
