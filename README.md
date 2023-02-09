# React with typescript note

## 型別定義範例

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

### 更多物件型別說明

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


## Useful React Prop Type Examples 實用 React Props 範例

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

 ### JSX.Element vs React.ReactNode

* JSX.Element -> Return value of React.createElement
* React.ReactNode -> Return value of a component


## Types of Interfaces

使用 type or Interface ?

[orta] (https://twitter.com/orta/status/1356129195835973632?s=20) 推薦直接使用 interface 直到你需要 type

https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/basic_type_example/

### 更多建議

一些實作的經驗法則

* 如果在寫一些第三方套件，都使用 interface 來定義型別，這樣方便使用者在缺少某些型別的時候，可以夠過 extend 來自己擴展型別 
* 撰寫 React 元件的時候，盡量使用 type 來定義 props 和 state，這樣可以保持元件的一致性並且更有約束性

(更多經驗法則)[https://medium.com/@martin_hotell/interface-vs-type-alias-in-typescript-2-7-2a8f1777af4c]
(TS 手冊也包含了一些撰寫規範)[https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces]

> 面對大型專案，使用 interfaces 效能比較好
> [https://github.com/microsoft/TypeScript/wiki/Performance#preferring-interfaces-over-intersections] (https://github.com/microsoft/TypeScript/wiki/Performance#preferring-interfaces-over-intersections) 但是有一些討論對此有一些[保留態度](https://news.ycombinator.com/item?id=25201887)


https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/basic_type_example/