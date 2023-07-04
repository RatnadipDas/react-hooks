# React Hooks

## Builtin Hooks

### [`useMemo`](https://react.dev/reference/react/useMemo) Hook
`useMemo` Hook lets you cache the result of a calculation between re-renders.
```tsx
const cachedValue = useMemo(calculateValue, dependencies)
```

#### Example
Consider the following react code:

```tsx
import { useMemo, useState } from "react";

type ThemeStyle = {
  backgroundColor: string;
  color: string;
};


const App = () => {
  const [number, setNumber] = useState<number>(0);
  const [dark, setDark] = useState<boolean>(false);
  const resultedNumber = slowFunction(number);
  const themeStyle: ThemeStyle = {
    backgroundColor: dark ? "black" : "white",
    color: dark ? "white" : "black",
  };

  return (
    <>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(parseInt(e.target.value))}
      />
      <button onClick={() => setDark((prevDark) => !prevDark)}>
        Change Theme
      </button>
      <div style={themeStyle}>{resultedNumber}</div>
    </>
  );
}

const slowFunction = (num: number) => {
  console.log("Calling Slow Function");
  for (let i = 0; i <= 2000000000; i++) {
    num++;
  }

  return num;
};

export default App;
```

Every time we change the theme of the application using `useState` Hook, it triggers the re-renders of the whole `App` component, and `slowFunction` is re-calculated during each re-render even though the `number` doesn't change; as a result, the theme updates with some noticeable delay. To avoid re-calculation of the `slowFunction` during each re-render, we will wrap the execution of `slowFunction` inside `useMemo` Hook with `number` inside the dependency array. Now, during each re-render, if `number` value doesn't change `resultedNumber` will use the previously cached value from the `slowFunction`, and we can change the theme with almost no delay.

```tsx
import { useMemo, useState } from "react";

type ThemeStyle = {
  backgroundColor: string;
  color: string;
};

const App = () => {
  const [number, setNumber] = useState<number>(0);
  const [dark, setDark] = useState<boolean>(false);
  const resultedNumber = useMemo<number>(() => slowFunction(number), [number]);
  const themeStyle: ThemeStyle = {
    backgroundColor: dark ? "black" : "white",
    color: dark ? "white" : "black",
  };

  return (
    <>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(parseInt(e.target.value))}
      />
      <button onClick={() => setDark((prevDark) => !prevDark)}>
        Change Theme
      </button>
      <div style={themeStyle}>{resultedNumber}</div>
    </number>
  );
}

const slowFunction = (num: number) => {
  console.log("Calling Slow Function");
  for (let i = 0; i <= 2000000000; i++) {
    num++;
  }

  return num;
};

export default App;
```

Now, suppose we `console.log` a message every time we change the theme of the application:

```tsx
import { useEffect, useMemo, useState } from "react";

type ThemeStyle = {
  backgroundColor: string;
  color: string;
};

const App = () => {
  const [number, setNumber] = useState<number>(0);
  const [dark, setDark] = useState<boolean>(false);
  const resultedNumber = useMemo<number>(() => slowFunction(number), [number]);
  const themeStyle: ThemeStyle = {
    backgroundColor: dark ? "black" : "white",
    color: dark ? "white" : "black",
  };

  useEffect(() => {
    console.log("Theme has changed!");
  }, [themeStyle]);

  return (
    <>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(parseInt(e.target.value))}
      />
      <button onClick={() => setDark((prevDark) => !prevDark)}>
        Change Theme
      </button>
      <div style={themeStyle}>{resultedNumber}</div>
    </>
  );
}

const slowFunction = (num: number) => {
  console.log("Calling Slow Function");
  for (let i = 0; i <= 2000000000; i++) {
    num++;
  }

  return num;
};

export default App;
```

But the problem is `useEffect` Hook with `themeStyle` will also run every time the `App` component re-renders because of the change of the reference of the `themeStyle`. To make sure that we only `console.log` message `"Theme has changed!"` when the actual value of the  `themeStyle` changes, not the reference, we can wrap the `themeStyle` inside `useMemo` Hook with `dark` inside the dependency array.

```tsx
import { useEffect, useMemo, useState } from "react";

type ThemeStyle = {
  backgroundColor: string;
  color: string;
};

const App = () => {
  const [number, setNumber] = useState<number>(0);
  const [dark, setDark] = useState<boolean>(false);
  const resultedNumber = useMemo<number>(() => slowFunction(number), [number]);
  const themeStyle = useMemo<ThemeStyle>(
    () => ({
      backgroundColor: dark ? "black" : "white",
      color: dark ? "white" : "black",
    }),
    [dark]
  );

  useEffect(() => {
    console.log("Theme has changed!");
  }, [themeStyle]);

  return (
    <>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(parseInt(e.target.value))}
      />
      <button onClick={() => setDark((prevDark) => !prevDark)}>
        Change Theme
      </button>
      <div style={themeStyle}>{resultedNumber}</div>
    </>
  );
}

const slowFunction = (num: number) => {
  console.log("Calling Slow Function");
  for (let i = 0; i <= 2000000000; i++) {
    num++;
  }

  return num;
};

export default App;
```

### [`useCallback`](https://react.dev/reference/react/useCallback) Hook
`useCallback` Hook lets you cache a function definition between re-renders.

```tsx
const cachedFn = useCallback(fn, dependencies)
```

#### Example
Consider the `App` component and `List` component:

```tsx
import { useCallback, useState } from "react";
import List from "./components/List";

const App = () => {
  const [number, setNumber] = useState<number>(0);
  const [dark, setDark] = useState<boolean>(false);
  const getItems = (incrementor: number) => {
    return [number + incrementor, number + incrementor + 1, number + incrementor + 2];
  };

  const theme = {
    backgroundColor: dark ? "#333" : "#fff",
    color: dark ? "#fff" : "#333",
  };

  return (
    <div style={theme}>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(parseInt(e.target.value))}
      />
      <button onClick={() => setDark((prevDark) => !prevDark)}>
        Change Theme
      </button>
      <List getItems={getItems} />
    </div>
  );
};

export default App;
```

```tsx
import { useEffect, useState } from "react";

type Props = {
  getItems: (incrementor: number) => number[];
};

const List = ({ getItems }: Props) => {
  const [items, setItems] = useState<number[]>([]);
  useEffect(() => {
    setItems(getItems(5));
    console.log("Updating items...");
  }, [getItems]);

  return items.map((item) => <div key={item}>{item}</div>);
};

export default List;
```

The problem with the above code is when we change the theme using `useState` inside `App` component, it triggers the re-rendering of the whole app. As a result, the reference of the `getItems` function changes and a change in reference of the `getItems` triggers the `useEffect` in the `List` component.

To get rid of the above problem, we can wrap function definition of `getItems` inside the `useCallback` Hook with `number` inside the dependency array of the `useCallback`. Now, if the value of the `number` variable doesn't change cached function definition for `getItems` with be used, which solves our problem.

```tsx
import { useCallback, useState } from "react";
import List from "./components/List";

const App = () => {
  const [number, setNumber] = useState<number>(0);
  const [dark, setDark] = useState<boolean>(false);
  const getItems = useCallback<(incrementor: number) => number[]>(
    return [number + incrementor, number + incrementor + 1, number + incrementor + 2];
  }, [number]);

  const theme = {
    backgroundColor: dark ? "#333" : "#fff",
    color: dark ? "#fff" : "#333",
  };

  return (
    <div style={theme}>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(parseInt(e.target.value))}
      />
      <button onClick={() => setDark((prevDark) => !prevDark)}>
        Change Theme
      </button>
      <List getItems={getItems} />
    </div>
  );
};

export default App;
```

```tsx
import { useEffect, useState } from "react";

type Props = {
  getItems: (incrementor: number) => number[];
};

const List = ({ getItems }: Props) => {
  const [items, setItems] = useState<number[]>([]);
  useEffect(() => {
    setItems(getItems(5));
    console.log("Updating items...");
  }, [getItems]);

  return items.map((item) => <div key={item}>{item}</div>);
};

export default List;
```