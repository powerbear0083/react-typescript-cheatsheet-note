# React with typescript note

## 元件 props 型別
### 型別定義範例

```javascript

type ObjArr2 = {
    id: string;
    title: string;
}

type AppProps = {
  message: string;
  count: number;
  disabled: boolean;
  /** array of a type! */
  // 陣列型別
  names: string[];
   /** string literals to specify exact string values, with a union type to join them together */
  // 使用 union type 將確定會使用的字串連結在一起
  status: "waiting" | "success";
  /** an object with known properties (but could have more at runtime) */
  // 已知 properties name 的物件型別
  obj: {
    id: string;
    title: string;
  };
  /** array of objects! (common) */
  // array object 的型別
  objArr: {
    id: string;
    title: string;
  }[];
  // 也可以這樣定義 array object 型別
  objArr2: ObjArr2[];
  /** any non-primitive value - can't access any properties (NOT COMMON but useful as placeholder) */
  // 任何非 non-primitive (非原始型別) 無法存取任何屬性 (不常見，看可以拿來做 placeholder 使用)
  obj2: object;
  /** an interface with no required properties - (NOT COMMON, except for things like `React.Component<{}, State>`) */
  // 沒有必須屬性的 interface (不常見，通常是給  `React.Component<{}, State>` 使用)
  obj3: {};
  /** a dict object with any number of properties of the same type */
  // 有任意數量且相同 properties 的 dict object
  dict1: {
    [key: string]: MyTypeHere;
  };
  // equivalent to dict1
  // 這個寫法等同上一個
  dict2: Record<string, MyTypeHere>; 
  /** function that doesn't take or return anything (VERY COMMON) */
  // 定義一個 function 且沒有回傳任何值 (很常見)
  onClick: () => void;
  /** function with named prop (VERY COMMON) */
  // 定義一個有傳入參數的 function (很常見)
  onChange: (id: number) => void;
  /** function type syntax that takes an event (VERY COMMON) */
  // 定義一個有 event 事件的 change function，這邊看到的是在 input element 的 change 事件
  onChange: (event: React.ChangeEvent<HTMLInputElement>) => void;
  /** alternative function type syntax that takes an event (VERY COMMON) */
  // 定義一個有 event 事件的 click function，這邊看到的是在 button element 的 change 事件
  onClick(event: React.MouseEvent<HTMLButtonElement>): void;
  /** any function as long as you don't invoke it (not recommended) */
  // any function 只要你不去調用他 ( 不推薦 )
  onSomething: Function;
  /** an optional prop (VERY COMMON!) */
  // 選填的 props (很常見)
  optional?: OptionalType;
  /** when passing down the state setter function returned by `useState` to a child component. `number` is an example, swap out with whatever the type of your state */
  // 將 useState 的 set[method] 傳給子元件的型別寫法，這邊看到的是 set[method] 是一個 number type
  setState: React.Dispatch<React.SetStateAction<number>>;
};

```

#### 更多物件型別說明

* 大多數的情況下，你都必須指定物件的型別如下例
  
```javascript
const obj = { 
  id: string; 
  name: string 
}
```

* 但是如果 object 沒有固定結構的話可使用 Record 這個縮寫
* 但是如果 object 有確定的 type 但是 key 會改變或者結構會改變可使用  generics 泛型
* Map 的數據結構在 React 不常見，因為 Map 是 mutable 可變數據結構，但是 React 偏好不變的數據
* ("Vague" object types ) “模糊”對像類型，如 object、{} 相當小眾，應該很少使用，並且功能可能與您預期的不同。
* JS 中的 Object 是  non-primitive value ，這包括 function、array、constructors


### Useful React Prop Type Examples 實用 React Props 範例

* 元件 Props 範例

```javascript
export declare interface AppProps {
  children?: React.ReactNode; // best, accepts everything React can render ，最好的方式，接受任何東西，React 都可以渲染
  childrenElement: JSX.Element; // A single React element ， 只接受 React Element
  style?: React.CSSProperties; // to pass through style props  ， 傳入 style props
  onChange?: React.FormEventHandler<HTMLInputElement>; // form events! the generic parameter is the type of event.target ， form event ，通用參數型別為 event target
  //  more info: https://react-typescript-cheatsheet.netlify.app/docs/advanced/patterns_by_usecase/#wrappingmirroring
  props: Props & React.ComponentPropsWithoutRef<"button">; // to impersonate all the props of a button element and explicitly not forwarding its ref ， 模擬 button element 但是沒有 ref
  props2: Props & React.ComponentPropsWithRef<MyButtonWithForwardRef>; // to impersonate all the props of MyButtonForwardedRef and explicitly forwarding its ref ， 模擬 button element 使用 ref
}
```

 #### JSX.Element vs React.ReactNode

* JSX.Element -> Return value of React.createElement
* React.ReactNode -> Return value of a component


### Types of Interfaces

使用 type or Interface ?

[orta] (https://twitter.com/orta/status/1356129195835973632?s=20) 推薦直接使用 interface 直到你需要 type

https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/basic_type_example/

#### 更多建議

一些實作的經驗法則

* 如果在寫一些第三方套件，都使用 interface 來定義型別，這樣方便使用者在缺少某些型別的時候，可以夠過 extend 來自己擴展型別 
* 撰寫 React 元件的時候，盡量使用 type 來定義 props 和 state，這樣可以保持元件的一致性並且更有約束性

(更多經驗法則)[https://medium.com/@martin_hotell/interface-vs-type-alias-in-typescript-2-7-2a8f1777af4c]
(TS 手冊也包含了一些撰寫規範)[https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces]

> 面對大型專案，使用 interfaces 效能比較好
> [https://github.com/microsoft/TypeScript/wiki/Performance#preferring-interfaces-over-intersections] (https://github.com/microsoft/TypeScript/wiki/Performance#preferring-interfaces-over-intersections) 但是有一些討論對此有一些[保留態度](https://news.ycombinator.com/item?id=25201887)

下面範例是一種很常用的型別寫法

```typescript
type MyType = TypeA | TypeB
```

但是使用 interfaces 來宣告 type 會更容易 implementing or extending 

### Types vs interfaces

這是一份很微妙的比較，看看就好，不用想太多

| Aspect                                     | Type | Interface |
| 可以描述 functions (Can describe functions) | V    |  V        |
| 可以描述 (constructors)                     | V    |  V        |
| 可以描述 (tuples)                           | V    |  V        |
| interfaces 可擴展                           | !    |  V        |
| classes 可擴展                              | X    |  V |
| classes 可實作 implements |  ! |  V |
| 可使用交集型別 Intersection Types |  V |  ! |
| 可建立聯合型別 union types |  V |  X |
| 可用來建立 mapped types |  V |  X |
| Can be mapped over with mapped types |  V |  V |
| Expands in error messages and logs |  V |  X |
| Can be augmented |  X |  V |
| Can be recursive |  ! |  V |

https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/basic_type_example/

### mapped types

https://www.typescriptlang.org/docs/handbook/2/mapped-types.html

---

## Function Components

下面的範例可以用來定義一般 function props 的型別並且 return JSX element

```typescript
// Declaring type of props - see "Typing Component Props" for more examples
// 宣告元件 props 的型別，可以查看 "元件 props 型別" 會有更多範例
type AppProps = {
  message: string;
}; 
/* use `interface` if exporting so that consumers can extend */
/* 使用 'interface' 定義型別，如果你有 exporting 可以讓方便的人擴展型別*/

// Easiest way to declare a Function Component; return type is inferred.
// 一個比較簡單的 Function Component 宣告範例，由於沒有宣告 return type ，這邊 TS 會自行推斷
const App = ({ message }: AppProps) => <div>{message}</div>;

// you can choose annotate the return type so an error is raised if you accidentally return some other type
// 你可以選擇幫元件的 return 值增加型別，以便你不小心回傳其他型別會發生錯誤
const App = ({ message }: AppProps): JSX.Element => <div>{message}</div>;

// you can also inline the type declaration; eliminates naming the prop types, but looks repetitive
// 你也可以直接單行把元件的 props 型別寫上去，但是定義的名字會看起來有點重複
const App = ({ message }: { message: string }) => <div>{message}</div>;
```

> 可以使用 [VSCode](https://marketplace.visualstudio.com/items?itemName=paulshen.paul-typescript-toolkit) 的套件，自動解構元件的 props 型別

### 為什麼 React.FC 讓人灰心，什麼是 React.FunctionComponent / React.VoidFunctionComponent

你可能看過很多下面這類型的範例

```Typescript

const App: React.FunctionComponent<{ message: string }> = ({ message }) => (
  <div>{message}</div>
);

```

現在已經不鼓勵使用這種寫法 ( ```Typescript React.FunctionComponent``` or ```Typescript React.FC ```)
可以使用 [jscodeshift]( https://github.com/gndelia/codemod-replace-react-fc-typescript ) 移掉 React.FC 、 React.SFC

上面的那些寫法和一般 function 有些許的不同

* React.FunctionComponent 可以明確的回傳型別，而 normal function 就沒那麼明確，需要額外的註明回傳型別
* React.FunctionComponent 為靜態屬性 (properties ) displayName 、 proTypes 、 defaultProps 提供自動檢查型別
  * 在使用 defaultProps 會有一些問題，這邊有[更多詳細的討論](https://github.com/typescript-cheatsheets/react/issues/87)
* 在 React 18 版之前 ，React.FunctionComponent 為 children 提供一個不明確的型別定義，看以下範例；這樣引起了很激烈的討論，所以之後 React.FC 就被[ Create React App TypeScript template ](https://github.com/facebook/create-react-app/pull/8177) 移掉了。

#### (已廢棄) 使用 React.VoidFunctionComponent or React.VFC

在 types/react 16.9.48 增加了 React.VoidFunctionComponent or React.VFC 為了可以明確幫 children 定義型別，但是在 React18 這兩種用法確被廢棄了，所以在 React 18+ 以上
都不推薦這兩種寫法。
可使用一般的 function 或者 React.FC 來代替

```typescript
type Props = { foo: string };

// OK now, in future, error
// 現在 ok 未來會報錯
const FunctionComponent: React.FunctionComponent<Props> = ({
  foo,
  children,
}: Props) => {
  return (
    <div>
      {foo} {children}
    </div>
  ); // OK
};

// Error now, in future, deprecated
// 現在會報錯，未來會廢棄
const VoidFunctionComponent: React.VoidFunctionComponent<Props> = ({
  foo,
  children,
}) => {
  return (
    <div>
      {foo}
      {children}
    </div>
  );
};
```

* 在未來 props 可能會被標註為 readonly ，但是 props 又可被解構，這是一個有爭議的問題

在大多數的情況，使用那種語法都沒有差，但是你可能會比較喜歡 React.FunctionComponent

### 小陷阱 ( Minor Pitfalls )

這種模式不再 supported

#### Conditional rendering

```typescript

const MyConditionalComponent = ({ shouldRender = false }) =>
  shouldRender ? <div /> : false; // 也不要在 JS 這樣做 don't do this in JS either
const el = <MyConditionalComponent />; // throws an error

```

這個會報錯的原因，是因為 function components 只能 return JSX Element 或者 null ，不然 TS 會發出一個 error message ，指出這個元件，不可 assignable　to element

#### Array.fill

```typescript
const MyArrayComponent = () => Array(5).fill(<div />);
const el2 = <MyArrayComponent />; // throws an error

```

Array.fill 會回傳一個陣列，這種寫法 TS 無法編譯，如果需要寫法，可以參考下面

```typescript
const MyArrayComponent = () => Array(5).fill(<div />) as any as JSX.Element;

```

---

## Hooks

從 @types/react v16.8 以上的版本 開始支援 Hooks


### useState

型別推斷 (Type inference) 用在簡單的值是很有效的

```typescript
const [state, setState] = useState(false);
// `state` 會被推斷為 boolean
// `state` is inferred to be a boolean
// `setState` 只接受 boolean
// `setState` only takes booleans

```

也可以查看 [Using inferred Types](https://react-typescript-cheatsheet.netlify.app/docs/basic/troubleshooting/types/#using-inferred-types) 章節，如果你需要更多複雜的型別

在開發過程中使用 Hooks 時，你可能需要將 null (null-ish) 設定為初始值， 為了明確定義 Hooks 的初始型別，可以使用 union type 來定義初始值

```typescript

const [user, setUser] = useState<User | null>(null);

// later...
setUser(newUser);

```

如果一個狀態在設定後，很快就被初始化並且在之後總是有一個值，可以使用 type assertions (型別斷言)

```typescript
const [user, setUser] = useState<User>({} as User);

// later...
setUser(newUser);

```

上面這種寫法只是暫時欺騙 TS ，讓 TS 在編譯的時候以為  {} 的型別是 User
正確的作法是，你應該把 user 裡面的所有型別都一個一個定義好
如果沒有這樣做，由於 user 的型別是依賴 User ，在 runtime 的時候可能會報錯

### useCallback

定義 useCallback 型別可以如下面的寫法

```typescript
const memoizedCallback = useCallback(
  (param1: string, param2: number) => {
    console.log(param1, param2)
    return { ok: true }
  },
  [...],
);
/**
 * VSCode will show the following type:
 * const memoizedCallback:
 *  (param1: string, param2: number) => { ok: boolean }
 */

```

在 React < 18 ，useCallback 的 function signature 預設為 any[] 型別 

```typescript
function useCallback<T extends (...args: any[]) => any>(
  callback: T,
  deps: DependencyList
): T;

```

在 React >= 18 ，useCallback 的 function signature 改為以下

```typescript
function useCallback<T extends Function>(callback: T, deps: DependencyList): T;

```

[function signature](https://developer.mozilla.org/en-US/docs/Glossary/Signature/Function) ：定義 function 或 methods 的 input 和 output 

signature 包含

* parameters and their types
* a return value and type
* exceptions that might be thrown or passed back
* information about the availability of the method in an object-oriented program (such as the keywords public, static, or prototype).

下面的程式碼在 React >= 18 會報錯，< 17 不會

```typescript
// @ts-expect-error Parameter 'e' implicitly has 'any' type.
useCallback((e) => {}, []);
// Explicit 'any' type.
useCallback((e: any) => {}, []);

```

### useReducer

使用 [Discriminated Unions](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html#discriminated-unions) 定義 reducer actions，
不要忘記定義 reducer 的 return 型別，否則 TS 會自動推斷他的行別

```typescript
import { useReducer } from "react";

const initialState = { count: 0 };

type ACTIONTYPE =
  | { type: "increment"; payload: number }
  | { type: "decrement"; payload: string };

function reducer(state: typeof initialState, action: ACTIONTYPE) {
  switch (action.type) {
    case "increment":
      return { count: state.count + action.payload };
    case "decrement":
      return { count: state.count - Number(action.payload) };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "decrement", payload: "5" })}>
        -
      </button>
      <button onClick={() => dispatch({ type: "increment", payload: 5 })}>
        +
      </button>
    </>
  );
}

```

[參考範例](https://www.typescriptlang.org/play?#code/LAKFEsFsAcHsCcAuACAVMghgZ2QJQKYYDGKAZvLJMgOTyEnUDcooRsAdliuO+IuBgA2AZUQZE+ZAF5kAbzYBXdogBcyAAwBfZmBCIAntEkBBAMIAVAJIB5AHLmAmgAUAotOShkyAD5zkBozVqHiI6SHxlagAaZGgMfUFYDAATNXYFSAAjfHhNDxAvX1l-Q3wg5PxQ-HDImLiEpNTkLngeAHM8ll1SJRJwDmQ6ZIUiHIAKLnEykqNYUmQePgERMQkY4n4ONTMrO0dXAEo5T2aAdz4iAAtkMY3+9gA6APwj2ROvImxJYPYqmsRqCp3l5BvhEAp4Ow5IplGpJhIHjCUABqTB9DgPeqJFLaYGfLDfCp-CIAoEFEFeOjgyHQ2BKVTNVb4RF05TIAC0yFsGWy8Fu6MeWMaB1x5K8FVIGAUglUwK8iEuFFOyHY+GVLngFD5Bx0Xk0oH13V6myhplZEm1x3JbE4KAA2vD8DFkuAsHFEFcALruAgbB4KAkEYajPlDEY5GKLfhCURTHUnKkQqFjYEAHgAfHLkGb6WpZI6WfTDRSvKnMgpEIgBhxTIJwEQANZSWRjI5SdPIF1u8RXMayZ7lSphEnRWLxbFNagAVmomhF6fZqYA9OXKxxM2KQWWK1WoTW643m63pB2u+7e-3SkEQsPamOGik1FO55p08jl6vdxuKcvv8h4yAmhAA)


### 使用 redux 的 Reducer

如果你使用 redux 撰寫 reducer function ，redux  會提供一個 ``` Reducer<AppState, Action> ``` 格式，它會幫你處理回傳的型別

上面的列子會就變成下面的範例
 
```typescript
import { Reducer } from 'redux';

export function reducer: Reducer<AppState, Action>() {}

```

## useEffect / useLayoutEffect

``` useEffect ``` and ``` useLayoutEffect ``` 都是用在 side effects 並且 return 可選的 cleanup function。
這表示假設他們不回傳任何型別，就沒有必要寫型別。
使用 useEffect 要注意，不要 return  function  或者 undefined 以外的內容，否則  TS 和 React 都會報錯。
當使用 arrow function 以下是一個很微妙的例子

```typescript
function DelayedEffect(props: { timerMs: number }) {
  const { timerMs } = props;

  useEffect(
    () =>
      setTimeout(() => {
        /* do stuff */
      }, timerMs),
    [timerMs]
  );
  // bad example! setTimeout implicitly returns a number
  // 不好的範例，setTimeout 隱含的回傳一個  number
  // because the arrow function body isn't wrapped in curly braces
  // 因為 useEffect 的 arrow function 沒有使用大括號
  return null;
}

// 以下修正上面的範例

function DelayedEffect(props: { timerMs: number }) {
  const { timerMs } = props;

  useEffect(() => {
    setTimeout(() => {
      /* do stuff */
    }, timerMs);
  }, [timerMs]);
  // better; use the void keyword to make sure you return undefined
  // 更好的方式，使用 void keyword 去確保 return undefined
  return null;
}

```

## useRef

在 TS 中 useRef return 一個 read-only or 可變 (mutable)的參考 (reference)，
這個取決於你的參數類型是否完全覆蓋初始值，選擇一個合適的來使用。(看無 QQ)
(意思是 useRef 的型別是看一開始你設定值是 ready-only( DOM element id ?) or mutable ( 滾動窗的元素 ?)

### DOM element ref

為了存取 DOM 元素：只提供元素類型作參數，並使用 null 作為初始值，在這種情況下，React 的 .current 回傳的參考就是一個 ready-only。
這種使用方法，TS 會以為你會將 ref 使用在 element 上面

```typescript
function Foo() {
  // - If possible, prefer as specific as possible. For example, HTMLDivElement
  // - is better than HTMLElement and way better than Element.
  // - Technical-wise, this returns RefObject<HTMLDivElement>
  // - 如果可以，盡可能的把 useRef 的元素寫清楚
  // - 如：HTMLDviElement 會比  HTMLElement 好，而且也比 Element 好
  // - 技術方面，這會回傳 RefObject<HTMLDivElement>
  const divRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    // Note that ref.current may be null. This is expected, because you may
    // conditionally render the ref-ed element, or you may forgot to assign it
    // 注意， ref.current 可能是空的，這是可以預期的，因為你在 render 的時候去判斷
    // 元素要不要渲染，或者你忘記 assign
    if (!divRef.current) throw Error("divRef is not assigned");

    // Now divRef.current is sure to be HTMLDivElement
    // 這時候 divRef.current 是 HTMLDivElement
    doSomethingWith(divRef.current);
  });

  // Give the ref to an element so React can manage it for you
  // 給 ref 一個元素，React 就會取得 div 這個元素
  return <div ref={divRef}>etc</div>;
}

```

如果你可以保證 divRef 永遠不會是 null，也可以把  if (!divRef.current) 拿掉
修改後的範例如下

```typescript
const divRef = useRef<HTMLDivElement>(null!);
// Later... No need to check if it is null
// 之後就不需要判斷 divRef.current 是否存在
doSomethingWith(divRef.current);

```

注意，如果你忘記把 ref assign 給頁面的 element ，你會在 runtime 的時候遇到錯誤

#### TIP 使用何種 HTML Element

Refs 的需要特殊性，下面的範例 inputEl 單純把型別設為 HTMLElement 是不夠的
這樣會發生 intentionally type error ，如果你不知道 element type 可以(查這裡) [https://github.com/microsoft/TypeScript/blob/v3.9.5/lib/lib.dom.d.ts#L19224-L19343]

```typescript
  const inputEl = React.useRef<HTMLElement>(null);
  <input ref={inputEl} />

```


### 可變的 (Mutable value) ref

[一個可變的值](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables)： 提供你想要的型別，並且確保初始值完全屬於該類型

```typescript

function Foo() {
  // Technical-wise, this returns MutableRefObject<number | null>
  // 技術上來說，這會回傳一個 MutableRefObject
  const intervalRef = useRef<number | null>(null);

  // You manage the ref yourself (that's why it's called MutableRefObject!)
  // 你可以自己管理控制 (所以才會叫做 MutableRefObject )
  useEffect(() => {
    intervalRef.current = setInterval(...);
    return () => clearInterval(intervalRef.current);
  }, []);

  // The ref is not passed to any element's "ref" prop
  // 這個 ref 不會給 element 傳任何 ref 的 prop
  return <button onClick={/* clearInterval the ref */}>Cancel timer</button>;
}

```

### 看看其他討論

[Related issue by @rajivpunjabi ](https://github.com/typescript-cheatsheets/react/issues/388)
[Example from Stefan Baumgartner  ](https://fettblog.eu/typescript-react/hooks/#useref)


## (useImperativeHandle) [https://beta.reactjs.org/reference/react/useImperativeHandle]

這個答案來自 (Stackoverflow)[https://stackoverflow.com/questions/62210286/declare-type-with-react-useimperativehandle/69292925#69292925]

```typescript

// Countdown.tsx

// Define the handle types which will be passed to the forwardRef
// 定義一個事件型別給 forwardRef
export type CountdownHandle = {
  start: () => void;
};

type CountdownProps = {};

const Countdown = forwardRef<CountdownHandle, CountdownProps>((props, ref) => {
  useImperativeHandle(ref, () => ({
    // start() has type inference here
    // start() 型別 inference 在這裡
    start() {
      alert("Start");
    },
  }));

  return <div>Countdown</div>;
});

// The component uses the Countdown component
// 使用 Countdown component 的原件

import Countdown, { CountdownHandle } from "./Countdown.tsx";

function App() {
  const countdownEl = useRef<CountdownHandle>(null);

  useEffect(() => {
    if (countdownEl.current) {
      // start() has type inference here as well
      // start() 的型別 inference 也在這裡
      countdownEl.current.start();
    }
  }, []);

  return <Countdown ref={countdownEl} />;
}


```

### 看看其他討論


[Using ForwardRefRenderFunction](https://stackoverflow.com/questions/62210286/declare-type-with-react-useimperativehandle/62258685#62258685)

## Custom Hooks

如果你在自定義的 Hooks return 一個 Array ，你會希望避免型別的推斷，因為 TS 會將它定義為 union type (當你希望 array 內容每個都有自己的型別)
可以使用 [TS 3.4 const assertions](https://devblogs.microsoft.com/typescript/announcing-typescript-3-4/#const-assertions)

```typescript

import { useState } from "react";

export function useLoading() {
  const [isLoading, setState] = useState(false);
  const load = (aPromise: Promise<any>) => {
    setState(true);
    return aPromise.finally(() => setState(false));
  };
  // infers [boolean, typeof load] instead of (boolean | typeof load)[]
  // 使用 as const 型別會是  [boolean, typeof load] 而不是 (boolean | typeof load)[]
  return [isLoading, load] as const;
}

```

(在 TS Playground 查看)[https://www.typescriptlang.org/play?target=5#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgcilQ3wFgAoCpAD0ljkwFcA7DYCZuRgZyQBkIKACbBmAcwAUASjgBvCnDhoO3eAG1g3AcNFiANHF4wAyjBQwkAXTgBeRMRgA6HklPmkEzCgA2vKQG4FJRV4b0EhWzgJFAAFHBBNJAAuODjcRIAeFGYATwA+GRs8uSDFIzcLCRgoRiQA0rgiGEYoTlj4xMdMUR9vHIlpW2Lys0qvXzr68kUAX0DpxqRm1rgNLXDdAzDhaxRuYOZVfzgAehO4UUwkKH21ACMICG9UZgMYHLAkCEw4baFrUSqVARb5RB5PF5wAA+cHen1BfykaksFBmQA]

使用上述這個方法，當你在解構的時候，可以得到正確的 type

https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/hooks/

#### 替代 tuple type 的型別

如果你有 const assertions 的麻煩，你也可以重新宣告或定義新的型別


```typescript

import { useState } from "react";

export function useLoading() {
  const [isLoading, setState] = useState(false);
  const load = (aPromise: Promise<any>) => {
    setState(true);
    return aPromise.finally(() => setState(false));
  };
  return [isLoading, load] as [
    boolean,
    (aPromise: Promise<any>) => Promise<any>
  ];
}

```

如果有寫很多自訂義 Hooks ，自動帶入 tuples helper function 也很有用


```typescript

function tuplify<T extends any[]>(...elements: T) {
  return elements;
}

function useArray() {
  const numberValue = useRef(3).current;
  const functionValue = useRef(() => {}).current;
  return [numberValue, functionValue]; // type is (number | (() => void))[]
}

function useTuple() {
  const numberValue = useRef(3).current;
  const functionValue = useRef(() => {}).current;
  return tuplify(numberValue, functionValue); // type is [number, () => void]
}

```
React 團對建議，return 超過兩個值以上請適當適用 object 取代 tuples

### More Hooks + TypeScript reading

* (https://medium.com/@jrwebdev/react-hooks-in-typescript-88fce7001d0d)[https://medium.com/@jrwebdev/react-hooks-in-typescript-88fce7001d0d]  
* (https://fettblog.eu/typescript-react/hooks/#useref)[https://fettblog.eu/typescript-react/hooks/#useref]  

如果你有寫 React Hooks library ，不要忘了你應該把 type 曝露出來給使用者使用

### Example React Hooks + TypeScript Libraries

* (https://github.com/mweststrate/use-st8)[https://github.com/mweststrate/use-st8]
* (https://github.com/palmerhq/the-platform)[https://github.com/palmerhq/the-platform]
* (https://github.com/sw-yx/hooks)[https://github.com/sw-yx/hooks]

---

## Class Components
https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/class_components


---

## Typing defaultProps


### 你可能會需要 defaultProps

根據這個 (tweet)[https://twitter.com/dan_abramov/status/1133878326358171650]討論，defaultProps 最終可能會被棄用，可以查看下面的討論

* (Original tweet)[https://twitter.com/hswolff/status/1133759319571345408]
* (More info can also be found in this article)[https://medium.com/@matanbobi/react-defaultprops-is-dying-whos-the-contender-443c19d9e7f1]

會有這種事情的發生，是因為大家都會使用 object 的預設參數

#### Function Components

```typescript

type GreetProps = { age?: number };

const Greet = ({ age = 21 }: GreetProps) => // etc
```

#### Class Components

```typescript

type GreetProps = {
  age?: number;
};

class Greet extends React.Component<GreetProps> {
  render() {
    const { age = 21 } = this.props;
    /*...*/
  }
}

let el = <Greet age={3} />;
```

### Typing defaultProps

TS 在 3.0+ defaultProps 型別推斷 (inference) 有很大的進步，(但還是有些問題)[https://github.com/typescript-cheatsheets/react/issues/61]

#### Function Components

```typescript

// using typeof as a shortcut; note that it hoists!
// 使用 typeof 的偷懶方法；注意他會提升
// you can also declare the type of DefaultProps if you choose
// 也可以宣告 defaultProps 的 type
// e.g. https://github.com/typescript-cheatsheets/react/issues/415#issuecomment-841223219
type GreetProps = { age: number } & typeof defaultProps;

const defaultProps = {
  age: 21,
};

const Greet = (props: GreetProps) => {
  // etc
};
Greet.defaultProps = defaultProps;
```

(See this in TS Playground)[https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAKjgQwM5wEoFNkGN4BmUEIcARFDvmQNwBQdMAnmFnAOKVYwAKxY6ALxwA3igDmWAFxwAdgFcQAIyxQ4AXzgAyOM1YQCcACZYCyeQBte-VPVwRZqeCbOXrEAXGEi6cCdLgAJgBGABo6dXo6e0d4TixuLzgACjAbGXjuPg9UAEovAD5RXzhKGHkoWTgAHiNgADcCkTScgDpkSTgAeiQFZVVELvVqrrrGiPpMmFaXcytsz2FZtwXbOiA]

https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/default_props

對於 Class components 來說，(有幾種方法可宣告)[https://github.com/typescript-cheatsheets/react/pull/103#issuecomment-481061483] type，包括使用 Pick utility type ，
但是還是推薦 "反轉" props 

```typescript

type GreetProps = typeof Greet.defaultProps & {
  age: number;
};

class Greet extends React.Component<GreetProps> {
  static defaultProps = {
    age: 21,
  };
  /*...*/
}

// Type-checks! No type assertions needed!
// 型別檢查，不需要 type assertions
let el = <Greet age={3} />;
```

##### 和 JSX.LibraryManagedAttributes 作者細微差別

上面所講的對一般開發 APP 來說很棒，但是有時候你會想把定義好的 props export 出去，方便其他人可以使用。
這裡會有一個問題 GreetProps 的 age 是必填的，但是對於 defaultProps 來說不是。

這裡有一個需要注意的地方，(GreetProps 是內部的元件的 props 而不是開放給外部用的)[https://github.com/typescript-cheatsheets/react/issues/66#issuecomment-453878710]，你可以分別建立給外部用的 type 
或者是可以使用外部 JSX.LibraryManagedAttributes 第三方套件


```typescript

// internal contract, should not be exported out
// 外部使用的 type ，不應該 exported
type GreetProps = {
  age: number;
};

class Greet extends Component<GreetProps> {
  static defaultProps = { age: 21 };
}

// external contract
// 外部使用 type
export type ApparentGreetProps = JSX.LibraryManagedAttributes<
  typeof Greet,
  GreetProps
>;

```

這樣 type 會正常 work，但如果把滑鼠游標停在 ApparentGreetProps 上面會蠻嚇人的，
可以使用 ComponentProps 這個樣板功能

#### Consuming Props of a Component with defaultProps

元件有 defaultProps 可以看出哪些是必填項目，但實際上並沒有這樣

##### Problem Statement

底下是你想要做的

```typescript

interface IProps {
  name: string;
}
const defaultProps = {
  age: 25,
};
const GreetComponent = ({ name, age }: IProps & typeof defaultProps) => (
  <div>{`Hello, my name is ${name}, ${age}`}</div>
);
GreetComponent.defaultProps = defaultProps;

const TestComponent = (props: React.ComponentProps<typeof GreetComponent>) => {
  return <h1 />;
};

// Property 'age' is missing in type '{ name: string; }' but required in type '{ age: number; }'
// Property 在 IProps 缺少 'age' type ，但是在 defaultProps 是必填
const el = <TestComponent name="foo" />;

```

##### Solution

使用 JSX.LibraryManagedAttributes 定義一個適用的 utility 

```typescript

type ComponentProps<T> = T extends | React.ComponentType<infer P> | React.Component<infer P>
  ? JSX.LibraryManagedAttributes<T, P>
  : never;

const TestComponent = (props: ComponentProps<typeof GreetComponent>) => {
  return <h1 />;
};

// No error
const el = <TestComponent name="foo" />;

```

#### Misc Discussions and Knowledge 其他討論與知識

##### 為什麼 React.FC 破壞 defaultProps

可以查看底下的討論

* [https://medium.com/@martin_hotell/10-typescript-pro-tips-patterns-with-or-without-react-5799488d6680](https://medium.com/@martin_hotell/10-typescript-pro-tips-patterns-with-or-without-react-5799488d6680)
* [https://github.com/DefinitelyTyped/DefinitelyTyped/issues/30695](https://github.com/DefinitelyTyped/DefinitelyTyped/issues/30695)
* [https://github.com/typescript-cheatsheets/react/issues/87](https://github.com/typescript-cheatsheets/react/issues/87)


##### Typescript2.9 和更早版本

在 TS2.9 或更早的版本，有更多方法可以做到，但是底下是我們見過最好的建議的做法


```typescript

type Props = Required<typeof MyComponent.defaultProps> & {
  /* additional props here */
};

export class MyComponent extends React.Component<Props> {
  static defaultProps = {
    foo: "foo",
  };
}

```

我們之前推薦 TS 的 Partial type 的功能，
意思使當前的 interface 可以使用 Partial 的功能包住當前的 interface，
透過這樣的方法，可以擴展 defaultProps，這樣就不用改變任何 types

```typescript

interface IMyComponentProps {
  firstProp?: string;
  secondProp: IPerson[];
}

export class MyComponent extends React.Component<IMyComponentProps> {
  public static defaultProps: Partial<IMyComponentProps> = {
    firstProp: "default",
  };
}

```
使用上面這種方法，在使用 JSX.LibraryManagedAttributes 這個套件的時候，會有問題。
基本原因在於，當建立 JSX 的時候 compiler 會把 defaultProps 視為選填的。


https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/default_props/

--- 

## Forms and Events

如果效能不是問題，那 inlining handler function 是一個比較簡單的作法，因為可以使用 (type inference and contextual typing)[https://www.typescriptlang.org/docs/handbook/type-inference.html#contextual-typing]

```typescript

const el = (
  <button
    onClick={(event) => {
      /* event will be correctly typed automatically! */
    }}
  />
);

```

但是如果你需要把單獨把 event handler 抽出去，IDE 工具會幫忙，因為 @type 定義有很多種類型。
輸入你要找的內容，通常都會自動完成。
下面是 onChange 的例子

```typescript 

type State = {
  text: string;
};
class App extends React.Component<Props, State> {
  state = {
    text: "",
  };

  // typing on RIGHT hand side of =
  onChange = (e: React.FormEvent<HTMLInputElement>): void => {
    this.setState({ text: e.currentTarget.value });
  };
  render() {
    return (
      <div>
        <input type="text" value={this.state.text} onChange={this.onChange} />
      </div>
    );
  }
}

```

(View in the TypeScript Playground)[https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgcilQ3wFgAoCtAGxQGc64BBMMOJADxiQDsATRsnQwAdAGFckHrxgAeCnDgBvAL4AaBcs2KA9Drg8IcMDjB1tcblwBccOjCjAeAcwDcmlRQB8W8ovso3HAAvL6KilYwtgBE0R7ulH5wepYAnmBOznAQPIgAkgDiABIAKnAAFij8dsB8SNmYIZo5YpUu9aEAFEi2QhgiAGLQIACiAG4ysqUAsgAyeTxgAK4wI9RIIDJeAJS2YxC1IT5KFjDlwHQidEgwAMowgUidSpacUewiaEtQRDwwJSgoM4biIxihqEt6iptglFCpYXBfnUoJ1tmFwkQYN9cp0LIpZHxgGMvHjwrInMt4DB0khgtFItE4GCIbSlGcLlcHtwRJEVNkeK0qsDgmzzpcWm1gXydCSkuE4LIdITiRYYR4KCogA]

除了使用 React.FormEvent 和 Void 去輸入參數和 return value ，也可以將 type 類型用於 event handler

```typescript 

  // typing on LEFT hand side of =
  onChange: React.ChangeEventHandler<HTMLInputElement> = (e) => {
    this.setState({text: e.currentTarget.value})
  }

```

### 為什麼這兩種方法做同樣的事情

第一種方法使用 inferred method signature (e: React.FormEvent<HTMLInputElement>): void)
第二種方法使用強制執行 @type/react 提供的類型
React.ChangeEventHandler<> 只是 @type/react 提供的一個比較簡單的方法，你可以將這兩種想成有更多種型別推段的方法
但是無論是那種模式，都是很好的模式 (. See our Github PR for more.)[https://github.com/typescript-cheatsheets/react/pull/24]

在表單中使用不受控元件輸入 onSubmit

如果你不太關心事件的類型，你可以使用 React.SyntheticEvent 
如果你的目標表單有你自己想要存取的自定義 input 可以使用 type assertion

```typescript 

<form
  ref={formRef}
  onSubmit={(e: React.SyntheticEvent) => {
    e.preventDefault();
    const target = e.target as typeof e.target & {
      email: { value: string };
      password: { value: string };
    };
    const email = target.email.value; // typechecks!
    const password = target.password.value; // typechecks!
    // etc...
  }}
>
  <div>
    <label>
      Email:
      <input type="email" name="email" />
    </label>
  </div>
  <div>
    <label>
      Password:
      <input type="password" name="password" />
    </label>
  </div>
  <div>
    <input type="submit" value="Log in" />
  </div>
</form>

```

(View in the TypeScript Playground)[https://www.typescriptlang.org/play?#code/JYWwD]


當然，如果你任何重要的表單，你應該使用 (formik)[https://formik.org/] or (Reach Hook Form)[https://react-hook-form.com/]
他們都是使用 TS 撰寫的第三方套件

https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/forms_and_events/

## List of event type

| Event Type           | Description
| -------------------- | ----------
| AnimationEvent       | CSS Animations.
| ChangeEvent          | input, textarea, select 元入的 change 事件
| ClipboardEvent       | copy, cut, paste 事件
| CompositionEvent     | event 發生於使用者間接地輸入文字 ( 如：瀏覽器跳出 popup ，需要使用者輸入文字)
| DragEvent            | 拖拉事件 (如：滑鼠拖拉)
| FocusEvent           | 事件觸發於當元素取得或失去 focus
| FormEvent            | 事件觸發於 form 取得或失去，或者當表單的值改變或者 submitted
| InvalidEvent         | 當一個 input type 有一個限制值，如果輸入的值不符合限制，就會觸發 invalid event ( e.g <input type="number" max="10"> )
| KeyboardEvent        | 使用者使用鍵盤觸發的事件，每一個 event 描述都有單一的 key interaction
| MouseEvent           | 事件發生在使用者點擊裝置時發生 (e.g. mouse)
| PointerEvent         | 事件發生在使用者點與裝置互動時發生，如：滑鼠、觸控螢幕、觸控板；觸控螢幕還支援多點觸控。 除法你是針對就瀏覽器開發 (IE10 or Safari 12)，一般建議使用 PointerEvent。Extends UIEvent.
| TouchEvent           | 事件發生在使用者觸控裝置時發生。Extends UIEvent.
| TransitionEvent      | CSS Transitions. 沒有全部瀏覽器都支援。Extends UIEvent.
| UIEvent              | 滑鼠、觸控、Pointer 的基本事件
| WheelEvent           | 滑鼠滾輪事件，類似輸入設備。(注意：不要跟 scroll event 混淆了)
| SyntheticEvent       | 上述 event 的基礎 event ，不確定 event 的類型時使用


 
### 關於  inputEvent

你可能注意到了，上面都沒有 inputEvent 。
這是 TS 不支援 inputEvent，因為 inputEvent 本身就沒有完全支援瀏覽器，而且在其他的瀏覽器可能有會有不同的行為。
可以使用 KeyboardEvent 來取代 inputEvent

Sources

* https://github.com/microsoft/TypeScript/issues/29441
* https://developer.mozilla.org/en-US/docs/Web/API/InputEvent
* https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/input_event


---

## Context

### Basic Example


這是一個建立切換 theme 的範例

```typescript

import { createContext } from "react";

type ThemeContextType = "light" | "dark";

const ThemeContext = createContext<ThemeContextType>("light");

```

context 需要一個 provider 把子元件包起來，這樣子元件才能使用 context

```typescript

import { useState } from "react";

const App = () => {
  const [theme, setTheme] = useState<ThemeContextType>("light");

  return (
    <ThemeContext.Provider value={theme}>
      <MyComponent />
    </ThemeContext.Provider>
  );
};

```

調用 useContext 來訂閱取得 context 的值

```typescript

import { useContext } from "react";

const MyComponent = () => {
  const theme = useContext(ThemeContext);

  return <p>The current theme is {theme}.</p>;
};

```

### 沒有預設的 context value

如果你沒有任何的有意義的預設於，請把預設值設為 null

```typescript

import { createContext } from "react";

interface CurrentUserContextType {
  username: string;
}

const CurrentUserContext = createContext<CurrentUserContextType | null>(null);

const App = () => {
  const [currentUser, setCurrentUser] = useState<CurrentUserContextType>({
    username: "filiptammergard",
  });

  return (
    <CurrentUserContext.Provider value={currentUser}>
      <MyComponent />
    </CurrentUserContext.Provider>
  );
};

```

這時候 context 的型別可能會是 null，必須注意
如果你直接存取 'currentUser' 的值，TS 會報錯，因為 TS 會認為 'currentUser' 可能是 null
可以使用 optional chaining 來避免這個問題

```typescript

import { useContext } from "react";

const MyComponent = () => {
  const currentUser = useContext(CurrentUserContext);

  return <p>Name: {currentUser?.username}.</p>;
};

```

最好的方式，就是不要去檢查 null 是否存在，因為我們知道 context 不會是 null。
有一個方式，我們可以自訂定義一個 hook ，在這個 hook 去檢查 context 是否為 null
如果沒有提供 context ，就會拋出一個錯誤

```typescript

import { createContext } from "react";

interface CurrentUserContextType {
  username: string;
}

const CurrentUserContext = createContext<CurrentUserContextType | null>(null);

const useCurrentUser = () => {
  const currentUserContext = useContext(CurrentUserContext);

  if (!currentUserContext) {
    throw new Error(
      "useCurrentUser has to be used within <CurrentUserContext.Provider>"
    );
  }

  return currentUserContext;
};

```

在 runtime 的時候檢查型別的好處是，如果 provider 沒有正確的把元件包裝起來，可以清楚在 console 看到錯誤訊息
現在可以存取 'currentUser.username' ，而且不用檢查 null

```typescript

import { useContext } from "react";

const MyComponent = () => {
  const currentUser = useCurrentUser();

  return <p>Username: {currentUser.username}.</p>;
};

```

### Type assertion as an alternative

另一個避免去檢查是否為 null 的方式是，使用 type assertion
去跟 TS 說，你知道 context 不會是 null

```typescript
import { useContext } from "react";

const MyComponent = () => {
  const currentUser = useContext(CurrentUserContext);

  return <p>Name: {currentUser!.username}.</p>;
};

```

另一種方式是，使用一個空物件作為預設值
並將其轉為預期的 context 類型

```typescript

const CurrentUserContext = createContext<CurrentUserContextType>(
  {} as CurrentUserContextType
);

```

你也可以使用 non-null 的型別宣告來取得同樣的結過

```typescript

const CurrentUserContext = createContext<CurrentUserContextType>(null!);

```

當你不知道使用那種宣告方式時，在 runtime 的時候，可以檢查拋出的 type asserting


---

## forwardRef/createRef

可以查看 React 官方文件 (Hooks section useRef)[https://github.com/typescript-cheatsheets/react/blob/main/README.md#hooks] 的部分

createRef：

```typescript

import { createRef, PureComponent } from "react";

class CssThemeProvider extends PureComponent<Props> {
  private rootRef = createRef<HTMLDivElement>(); // like this
  render() {
    return <div ref={this.rootRef}>{this.props.children}</div>;
  }
}

```

forwardRef：

```typescript

import { forwardRef, ReactNode } from "react";

interface Props {
  children?: ReactNode;
  type: "submit" | "button";
}
export type Ref = HTMLButtonElement;

export const FancyButton = forwardRef<Ref, Props>((props, ref) => (
  <button ref={ref} className="MyClassName" type={props.type}>
    {props.children}
  </button>
));
```

#### Side note

從 forwardRef 得到的 ref 是可變的，因此你可以依據你要的 assigns 它

下面是一個刻意的範例，如果你想讓 ref 是不可變 (immutable) 的話，需要 assigns 值給 ref

```typescript

import { forwardRef, ReactNode, Ref } from "react";

interface Props {
  children?: ReactNode;
  type: "submit" | "button";
}

export const FancyButton = forwardRef(
  (
    props: Props,
    ref: Ref<HTMLButtonElement> // <-- here!
  ) => (
    <button ref={ref} className="MyClassName" type={props.type}>
      {props.children}
    </button>
  )
);

```

如果你要取得 props 傳過來的　components 請使用 (ComponentPropsWithRef)[https://github.com/DefinitelyTyped/DefinitelyTyped/blob/a05cc538a42243c632f054e42eab483ebf1560ab/types/react/index.d.ts#L770]

### 通用 forwardRefs

閱讀更多關於通用 forwardRefs 的資訊，請參考 [https://fettblog.eu/typescript-react-generic-forward-refs/]

#### Option 1 Wrapper component

```typescript

type ClickableListProps<T> = {
  items: T[];
  onSelect: (item: T) => void;
  mRef?: React.Ref<HTMLUListElement> | null;
};

export function ClickableList<T>(props: ClickableListProps<T>) {
  return (
    <ul ref={props.mRef}>
      {props.items.map((item, i) => (
        <li key={i}>
          <button onClick={(el) => props.onSelect(item)}>Select</button>
          {item}
        </li>
      ))}
    </ul>
  );
}

```

#### Option 2 重新宣告 forwardRef

```typescript

// Redecalare forwardRef
declare module "react" {
  function forwardRef<T, P = {}>(
    render: (props: P, ref: React.Ref<T>) => React.ReactElement | null
  ): (props: P & React.RefAttributes<T>) => React.ReactElement | null;
}

// Just write your components like you're used to!
import { forwardRef, ForwardedRef } from "react";

interface ClickableListProps<T> {
  items: T[];
  onSelect: (item: T) => void;
}

function ClickableListInner<T>(
  props: ClickableListProps<T>,
  ref: ForwardedRef<HTMLUListElement>
) {
  return (
    <ul ref={ref}>
      {props.items.map((item, i) => (
        <li key={i}>
          <button onClick={(el) => props.onSelect(item)}>Select</button>
          {item}
        </li>
      ))}
    </ul>
  );
}

export const ClickableList = forwardRef(ClickableListInner);

```

### More Info

* https://medium.com/@martin_hotell/react-refs-with-typescript-a32d56c4d315
* You may also wish to do (Conditional Rendering with forwardRef)[https://github.com/typescript-cheatsheets/react/issues/167].

---

## Portals

https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/portals

Using ReactDOM.createPortal

```typescript

const modalRoot = document.getElementById("modal-root") as HTMLElement;
// assuming in your html file has a div with id 'modal-root';
// 假設你的 html 檔案有一個 id 為 'modal-root' 的 div

export class Modal extends React.Component<{ children?: React.ReactNode }> {
  el: HTMLElement = document.createElement("div");

  componentDidMount() {
    modalRoot.appendChild(this.el);
  }

  componentWillUnmount() {
    modalRoot.removeChild(this.el);
  }

  render() {
    return ReactDOM.createPortal(this.props.children, this.el);
  }
}

```

(在 TS Playground 查看)[shorturl.at/fjwO9]

### Using Hooks

跟上面的範例一樣，只是使用 Hooks

```typescript

import { useEffect, useRef, ReactNode } from "react";
import { createPortal } from "react-dom";

const modalRoot = document.querySelector("#modal-root") as HTMLElement;

type ModalProps = {
  children: ReactNode;
};

function Modal({ children }: ModalProps) {
  // create div element only once using ref
  // 只建立一次 div element，使用 ref
  const elRef = useRef<HTMLDivElement | null>(null);
  if (!elRef.current) elRef.current = document.createElement("div");

  useEffect(() => {
    const el = elRef.current!; // non-null assertion because it will never be null
    modalRoot.appendChild(el);
    return () => {
      modalRoot.removeChild(el);
    };
  }, []);

  return createPortal(children, elRef.current);
}

```

(在 TS Playground 查看)[shorturl.at/fqruS]


Modal Component Usage Example

```typescript

import { useState } from "react";

function App() {
  const [showModal, setShowModal] = useState(false);

  return (
    <div>
      // you can also put this in your static html file
      // 你也可以把這個放在你的靜態 html 檔案中
      <div id="modal-root"></div>
      {showModal && (
        <Modal>
          <div
            style={{
              display: "grid",
              placeItems: "center",
              height: "100vh",
              width: "100vh",
              background: "rgba(0,0,0,0.1)",
              zIndex: 99,
            }}
          >
            I'm a modal!{" "}
            <button
              style={{ background: "papyawhip" }}
              onClick={() => setShowModal(false)}
            >
              close
            </button>
          </div>
        </Modal>
      )}
      <button onClick={() => setShowModal(true)}>show Modal</button>
      // rest of your app
    </div>
  );
}

```

### 範例脈絡

這個範例基礎是 React 官方文件的 (Event Bubbling Through Portals)[https://reurl.cc/8q83My] 列子

---

## Error Boundaries


### Option 1: Using react-error-boundary

(React-error-boundary)[https://github.com/bvaughn/react-error-boundary] 是一個 TS 有支援的輕量化套件
使用這個套件，還可以避免不在寫出 class component 這種不流行的寫法


### Options 2: Writing your custom error boundary component 撰寫自訂的 error boundary component

如果你不想使用新的套件，你可以自己撰寫一個 error boundary component

```typescript

import React, { Component, ErrorInfo, ReactNode } from "react";

interface Props {
  children?: ReactNode;
}

interface State {
  hasError: boolean;
}

class ErrorBoundary extends Component<Props, State> {
  public state: State = {
    hasError: false
  };

  public static getDerivedStateFromError(_: Error): State {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  public componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error("Uncaught error:", error, errorInfo);
  }

  public render() {
    if (this.state.hasError) {
      return <h1>Sorry.. there was an error</h1>;
    }

    return this.props.children;
  }
}

export default ErrorBoundary;


```


## Concurrent React/React Suspense

沒有寫任何東西，可以查這這裡 (https://github.com/sw-yx/brain)[https://github.com/sw-yx/brain]

---

## Troubleshooting Handbook: Types 問題排除：型別

> 你是否有閱讀過 (the TS FAQ)[https://github.com/microsoft/TypeScript/wiki/FAQ?] 你的問題應該可以在那裡找到答案

遇到奇怪的 error 問題時，這是使用 TS 寫 React 最困難的時候，但你並不孤單。
要有耐心，畢竟你是在學一個全新的語言，但是你越擅長這個，你跟 compiler error 奮戰的時間就會越少，
compiler 為你工作的時間就會越多。

避免使用 any type，盡可能的去體驗 TS 的優點，
讓我們試著熟悉一些解決問題的常見策略。


### Union Types and Type Guarding

union types 可以很方便的解決下面這種型別問題

```typescript

class App extends React.Component<
  {},
  {
    count: number | null; // like this
  }
> {
  state = {
    count: null,
  };
  render() {
    return <div onClick={() => this.increment(1)}>{this.state.count}</div>;
  }
  increment = (amt: number) => {
    this.setState((state) => ({
      count: (state.count || 0) + amt,
    }));
  };
}

```

(View in the TypeScript Playground)[https://reurl.cc/9VqqaO]


Type Guarding: 有時候 union types 解決了一個區去的問題，但是下其他地方 (downstream) 又產生了新的問題，
如果有兩個 A、B 兩個 Object，A | B 不是 A or B，而是兩個兼容，如果你預期是前一種，這裡會產生混亂。
了解一下如何撰寫 檢查、guards、assertions (可以參閱下面的條件渲染部分)。

```typescript

interface Admin {
  role: string;
}
interface User {
  email: string;
}

// Method 1: use `in` keyword
// Method 1: 使用 `in` 關鍵字
function redirect(user: Admin | User) {
  if ("role" in user) {
    // use the `in` operator for typeguards since TS 2.7+
    // 自 TS 2.7+ 使用 `in` 關鍵字做型別檢查
    routeToAdminPage(user.role);
  } else {
    routeToHomePage(user.email);
  }
}

// Method 2: custom type guard, does the same thing in older TS versions or where `in` isnt enough
// Method 2: 在舊版的 TS 或是 `in` 關鍵字不夠用的時候，可以自己撰寫 type guard
function isAdmin(user: Admin | User): user is Admin {
  return (user as any).role !== undefined;
}

```

(View in the TypeScript Playground)[https://reurl.cc/a1Dnv9]

Method 2 也被稱作為 User Defined Type Guards (UDTGs)[https://reurl.cc/XLbjW7]，這對於程式碼的閱讀性非常有幫助。
這是 TS 如何用 typeof 和 instabceof 作 refines types 的方式。

如果你需要 if...else 或 switch...case 的判斷方式，它應該可以正常運作
如果你需要幫助，可以查看 (Discriminated Unions )[https://reurl.cc/ZXoAWV]。
也可以查看  (Basarat's)[https://reurl.cc/vkxd6a] 的文章，這在使用 useReducer or reducer 很方便


### Optional Types

如果元件有選填的 props，可以使用 ? 表示，或者是使用 defaultProps

```typescript

class MyComponent extends React.Component<{
  message?: string; // like this
}> {
  render() {
    const { message = "default" } = this.props;
    return <div>{message}</div>;
  }
}

```

也可以使用 ! 來判斷 undefined 是否有這個值，但是不鼓勵這樣用


### Enum Types

我們推薦盡可能的避免使用 enum types。

使用 enums 當作型別會(有些的問題)[https://fettblog.eu/tidy-typescript-avoid-enums/] (TS team 也同意)[https://twitter.com/orta/status/1348966323271987201?s=20]。
一個簡單的替代方式是使用字串的 union types。

```typescript

export declare type Position = "left" | "right" | "top" | "bottom";

```

如果你真的有使用 enums types 的需求，要記得在 TS 中 enums 預設是 number。
你通常會將 enums types 轉換成字串。

```typescript

export enum ButtonSizes {
  default = "default",
  small = "small",
  large = "large",
}

// usage
export const PrimaryButton = (
  props: Props & React.HTMLProps<HTMLButtonElement>
) => <Button size={ButtonSizes.default} {...props} />;

```


### Type Assertion

有時候你比 TS 還更清楚知道你正在使用的型別，或者使用 union types 或者使用 as 關鍵字這樣更能更具體的其他 API 一起使用。
這告訴 compiler 你知道你正在做什麼。

```typescript

class MyComponent extends React.Component<{
  message: string;
}> {
  render() {
    const { message } = this.props;
    return (
      <Component2 message={message as SpecialMessageType}>{message}</Component2>
    );
  }
}
```

(View in the TypeScript Playground)[https://reurl.cc/LNxGM7]

注意你不能 assert 任何事情，基本上它比較適合用在 refining types，
因此他跟 "casting" type 不一樣。

你也可以 assert 屬性為非 non-null，當你要存取的時候

```typescript

// ! before the period 
// 有屬性之前
element.parentNode!.removeChild(element); 

// ! after the property accessing
// 有屬性之後存取
myFunction(document.getElementById(dialog.id!)!); 

// definite assignment assertion... be careful!
// 明確的賦值 assignment ... 要小心！
let userID!: string; 

```

當然實際處理 null 的情況不是 asserting


### Simulating Nominal Types ( 模擬標稱 Types )

TS 的結構 typing 很方便，但是有時候你不想一直重複撰寫 type
可以使用 simulate nominal typing 綁定 type 

(Nominal Typing)[https://basarat.gitbook.io/typescript/main-1/nominaltyping]

```typescript

type OrderID = string & { readonly brand: unique symbol };
type UserID = string & { readonly brand: unique symbol };
type ID = OrderID | UserID;

```

我們可以使用物件伴隨模 ( Companion Object Pattern ) 式來建立值

```typescript

function OrderID(id: string) {
  return id as OrderID;
}
function UserID(id: string) {
  return id as UserID;
}

```

現在 TS 將不允許你在錯誤的地方使用錯誤的 ID

```typescript

function queryForUser(id: UserID) {
  // ...
}

// Error, Argument of type 'OrderID' is not assignable to parameter of type 'UserID'
// 錯誤，type 'OrderID' 的 Argument 不能指派給型別 'UserID' 的參數
queryForUser(OrderID("foobar")); 

```

在未來可以使用 unique 的關鍵字來建立 nominal types  (See this PR.) [https://github.com/microsoft/TypeScript/pull/33038]


### Intersection Types

將兩個 types 結合在一起是很方便的事，舉個列子，當你的原件應該像 button 元件鏡射原本的 props 時

```typescript

export interface PrimaryButtonProps {
  label: string;
}
export const PrimaryButton = (
  props: PrimaryButtonProps & React.ButtonHTMLAttributes<HTMLButtonElement>
) => {
  // do custom buttony stuff
  return <button {...props}> {props.label} </button>;
};

```

(Playground here)[https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgcilQ3wFgAoCipAD0ljmADsYkpN0k4AFKUFKAE8AQgFcYMCE14QwAZzgBvCnDgAbFACMkagFxw5MPkwDmAbgoBfanWjw0Uwzz4gBI8ZKZwAvHAAUKnBgOPL6vPxCYhJSMvJwAGSIxDAAdFGeABIAKgCyADIAghJ8muJIcgA82fnpUgCiakggSCwAfBQAlD6tSoEA9H1wACYQcGiihrhwpdFMggYwopiYgUSLUF4VM55KKXvBsnKWPYoH8ika2mqWcBV921KtFuSWQA]

你也可以使用 Intersection Types 為類似的元件建立可覆用的元件


```typescript

type BaseProps = {
   className?: string,
   style?: React.CSSProperties
   name: string // used in both
}
type DogProps = {
  tailsCount: number
}
type HumanProps = {
  handsCount: number
}
export const Human = (props: BaseProps & HumanProps) => // ...
export const Dog = (props: BaseProps & DogProps) => // ...

```

(View in the TypeScript Playground)[https://www.typescriptlang.org/play?#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgcilQ3wFgAoCmATzCTgCEUBnJABRzGbgF44BvCnGFoANi2YA5FCCQB+AFxxmMKMAB2AcwA0Q4Suqj5S5OhgA6AMIBlaxwh1YwJMz1x1MpEpVqtcAPT+cACurAAmcBpwAEYQMAAWFAC+VLT0ACIQmvZcvAJ6MCjAosyWEMHqMErqwSDRSFDJqXRwABK1KOo53HyC5MLxnWGl5ZXVtfWN5CnkSAAekLBwaBDqKm0d6ibEFgBilgA8TKzdcABkGyCd3QB8eQAUAJS8d-d6B2HAAG4BNxSPFAo80W8BWa3gmU02zM5n2RxY7E43AukNuD2ePFe70+P38f3IjyAA]


不要將 Intersection Types ( which are and operations ) 與 Union Types ( which are or operations ) 搞混。


### Union Types

請個部分尚未撰寫，同時可以查看 (Union Types)[https://github.com/typescript-cheatsheets/react/blob/main/README.md#union-types-and-type-guarding] 的範例

ADVANCED 的 cheatsheet 也有關於 Discriminated Union Types，當 TS 沒有預期的那樣縮小 union type 時，這很有用。


### Overloading Function Types

具體來說，如果涉及到 function ，你可能需要的是 overload type 而不是 union type
常用撰寫 function type 的方式如下

```typescript

type FunctionType1 = (x: string, y: number) => number;

```

但是這樣不能使用 overload，如果你要實作 (implementation)，你可以用 function 的關鍵字將他們放在一起

```typescript

function pickCard(x: { suit: string; card: number }[]): number;
function pickCard(x: number): { suit: string; card: number };
function pickCard(x): any {
  // implementation with combined signature
  // ...
}

```

如果你沒有要實作 overloading function ，只是要定義 d.ts 檔案，
在這種情況底下，你可以使用 old-school 的方式來撰寫
關鍵是對 TS 來說，function 只是沒有 key 可呼叫的 object

```typescript

type pickCard = {
  (x: { suit: string; card: number }[]): number;
  (x: number): { suit: string; card: number };
  // no need for combined signature in this form
  // you can also type static properties of functions here eg `pickCard.wasCalled`
};

```
注意，當你要實際實作 overloading function 時，在實作時需要宣告 combined call signature 
TS 不會為了做型別推斷，可以看 DOM API 的範例  e.g. createElement.

(Read more about Overloading in the Handbook.)[https://www.typescriptlang.org/docs/handbook/functions.html#overloads]

### Using Inferred Types

https://react-typescript-cheatsheet.netlify.app/docs/basic/troubleshooting/types/