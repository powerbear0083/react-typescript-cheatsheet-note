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
  // 大部分都這樣定義 array object type
  objArr2: ObjArr2[];
  /** any non-primitive value - can't access any properties (NOT COMMON but useful as placeholder) */
  // 任何非 non-primitive (非原始型別) 無法存取任何屬性 (不常見，看可以拿來做 placeholder 使用)
  obj2: object;
  /** an interface with no required properties - (NOT COMMON, except for things like `React.Component<{}, State>`) */
  obj3: {};
  /** a dict object with any number of properties of the same type */
  dict1: {
    [key: string]: MyTypeHere;
  };
  dict2: Record<string, MyTypeHere>; // equivalent to dict1
  /** function that doesn't take or return anything (VERY COMMON) */
  onClick: () => void;
  /** function with named prop (VERY COMMON) */
  onChange: (id: number) => void;
  /** function type syntax that takes an event (VERY COMMON) */
  onChange: (event: React.ChangeEvent<HTMLInputElement>) => void;
  /** alternative function type syntax that takes an event (VERY COMMON) */
  onClick(event: React.MouseEvent<HTMLButtonElement>): void;
  /** any function as long as you don't invoke it (not recommended) */
  onSomething: Function;
  /** an optional prop (VERY COMMON!) */
  optional?: OptionalType;
  /** when passing down the state setter function returned by `useState` to a child component. `number` is an example, swap out with whatever the type of your state */
  setState: React.Dispatch<React.SetStateAction<number>>;
};

```

https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/basic_type_example