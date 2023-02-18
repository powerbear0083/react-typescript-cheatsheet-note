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

#### (以廢棄) 使用 React.VoidFunctionComponent or React.VFC

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

https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/hooks/