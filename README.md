# React Hooks

## [`useState`](https://react.dev/reference/react/useState) Hook
The `useState` Hook lets you add a state variable to your component.

```tsx
const [state, setState] = useState(initialState);
```

### Example
In the following React app, we have created three states with `useState` Hook. `useState` Hook returns us a variable which stores the current state (in the example code `number, inputName, names`) and a setter function which has a callback function which can accept current state as a parameter and return the new state (in the example code `setNumber, setInputName, setNames`), we can also change the current state without using the callback function by passing the new state in the setter function directly. Every time we set a new state using the setter function, React's virtual DOM re-renders the whole component.
```tsx

import { useState } from "react";

const App = () => {
  const [number, setNumber] = useState<number>(0);
  const [inputName, setInputName] = useState<string>("");
  const [names, setNames] = useState<string[]>([]);

  const handleSubmit = () => {
    setNames((prevNames) => [...prevNames, inputName]);
    setInputName("");
  };

  return (
    <>
      <input
        type="text"
        value={inputName}
        onChange={(event) => setInputName(event.target.value)}
      />
      <button onClick={handleSubmit}>Submit</button>
      <br />
      <ul>
        {names.map((name) => (
          <li key={name}>{name}</li>
        ))}
      </ul>
      <br />
      <button onClick={() => setNumber((prevNumber) => prevNumber - 1)}>
        -
      </button>
      <span>{number}</span>
      <button onClick={() => setNumber((prevNumber) => prevNumber + 1)}>
        +
      </button>
    </>
  );
};

export default App;
```

## [`useEffect`](https://react.dev/reference/react/useEffect) Hook

The `useEffect` Hook lets you synchronize a component with an external system.

```tsx
useEffect(setup, dependencies?);
```

### Example
In the following example of React app, we have created a timer using `useEffect` Hook.
`useEffect` Hook takes a setup function with our Effect logic which can returns a cleanup function, and this cleanup function is called when this `useEffect` hook is called the next time our component gets unmounted. `useEffect` Hook also takes dependencies inside the dependency array. If there are no dependencies inside of the dependency array, then the `useEffect` is called at the time of the component mounting and the cleanup function is called at the time of the component unmounting. If there are dependencies inside of the dependency array, then `useEffect` is run at the time of component mounting, and whenever the dependencies change, first the cleanup function is called, and then the logic present inside the `useEffect` is run. The cleanup also runs just before the unmounting of the component. Also, `useEffect` Hooks with no dependency arrays will run the cleanup function and subsequently, the `useEffect` logic on every re-render of the application.
```tsx
import { useEffect, useState } from "react";

const App = () => {
  const [current, setCurrent] = useState<Date>(new Date());
  const [hour, setHour] = useState<number>(current.getHours());
  const [minute, setMinute] = useState<number>(current.getMinutes());
  const [second, setSecond] = useState<number>(current.getSeconds());

  useEffect(() => {
    const interval = setInterval(() => {
      setCurrent(new Date());
    }, 1000);

    return () => {
      clearInterval(interval);
    };
  }, []);

  useEffect(() => {
    setHour(current.getHours());
    setMinute(current.getMinutes());
    setSecond(current.getSeconds());

    return () => {
      console.log("Cleanup function is called");
    };
  }, [current]);

  return (
    <div>
      <h1>
        <span>{hour}</span> : <span>{minute}</span> : <span>{second}</span>
      </h1>
    </div>
  );
};

export default App;
```

## [`useRef`](https://react.dev/reference/react/useRef) Hook

The `useRef` is a React Hook that lets you reference a value that’s not needed for rendering.

```tsx
const ref = useRef(initialValue);
```

### Example
The app below counts the number of times the app gets rendered using the `useRef` Hook.

```tsx
import { useState, useEffect, useRef } from "react";

const App = () => {
  const [name, setName] = useState<string>("");
  const renderCount = useRef<number>(2); // in strict mode of React v18 on first run app renders 2 times

  useEffect(() => {
    renderCount.current++;
  });

  return (
    <>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <div>My name is {name}</div>
      <div>I rendered {renderCount.current} time</div>
    </>
  );
};

export default App;
```

People mostly use the `useRef` Hooks to reference the HTML elements inside of the `JSX/TSX`:

```tsx
import { useState, useRef } from "react";

const App = () => {
  const [name, setName] = useState<string>("");
  const inputRef = useRef<HTMLInputElement>(null);

  const handleFocus = () => {
    inputRef.current?.focus();
  };

  return (
    <>
      <input
        ref={inputRef}
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <div>My name is {name}</div>
      <button onClick={handleFocus}>Focus</button>
    </>
  );
};

export default App;
```


## [`useLayoutEffect`](https://react.dev/reference/react/useLayoutEffect) Hook
The `useLayoutEffect` is a version of useEffect that fires before the browser repaints the screen.

```tsx
useLayoutEffect(setup, dependencies?);
```

### Example
```tsx
import { useState, useRef, useLayoutEffect } from "react";

const App = () => {
  const [show, setShow] = useState<boolean>(false);
  const popup = useRef<HTMLDivElement>(null);
  const button = useRef<HTMLButtonElement>(null);
  useLayoutEffect(() => {
    if (!(popup.current && button.current)) return;

    const { bottom } = button.current.getBoundingClientRect();
    popup.current.style.top = `${bottom + 25}px`;
  }, [show]);
  return (
    <>
      <button ref={button} onClick={() => setShow((s) => !s)}>
        Click Here
      </button>
      {show ? (
        <div ref={popup} style={{ position: "relative" }}>
          This is a popup
        </div>
      ) : null}
    </>
  );
};

export default App;
```

## [`useMemo`](https://react.dev/reference/react/useMemo) Hook
The `useMemo` Hook lets you cache the result of a calculation between re-renders.
```tsx
const cachedValue = useMemo(calculateValue, dependencies);
```

### Example
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

But the problem is `useEffect` Hook with `themeStyle` will also run every time the `App` component re-renders because of the change of the reference of the `themeStyle`. To make sure that we only `console.log` message `"Theme has changed!"` when the actual value of the  `themeStyle` changes, not the reference, we can wrap the `themeStyle` inside the `useMemo` Hook with `dark` inside the dependency array.

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

## [`useCallback`](https://react.dev/reference/react/useCallback) Hook
The `useCallback` Hook lets you cache a function definition between re-renders.

```tsx
const cachedFn = useCallback(fn, dependencies);
```

### Example
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

## [`useContext`](https://react.dev/reference/react/useContext) Hook

The `useContext` Hook lets you read and subscribe to context from your component.

```tsx
const value = useContext(SomeContext);
```

### Example
The app below describes an use case of `useContext` Hook. We will use `useContext` to provide global theme, getter method to get the current theme mode and setter method to change the current theme mode.

First inside the `Context` folder we will crate there files namely: `theme.d.ts, themeContext.ts, ThemeState.tsx`.

Contents of `theme.d.ts` file:
```tsx
export type Theme = "DARK" | "LIGHT";
export type ThemeStyle = {
  color: string;
  backgroundColor: string;
};
```

Contents of the `themeContext.ts` file:
```tsx
import { createContext, Dispatch, SetStateAction } from "react";
import { Theme, ThemeStyle } from "./theme";

type ThemeContextType = {
  theme: Theme;
  themeStyle: ThemeStyle;
  setTheme: Dispatch<SetStateAction<Theme>>;
};

const themeContext = createContext<ThemeContextType>({
  theme: "LIGHT",
  themeStyle: {
    color: "#333",
    backgroundColor: "#fff",
  },
  setTheme: () => {
    return;
  },
});

export default themeContext;
```

Contents of the `ThemeState.tsx` file:
```tsx
import { PropsWithChildren, useState } from "react";
import ThemeContext from "./themeContext";
import { Theme } from "./theme";

const ThemeState = ({ children }: PropsWithChildren) => {
  const [theme, setTheme] = useState<Theme>("DARK");
  const themeStyle = {
    color: theme === "DARK" ? "#fff" : "#333",
    backgroundColor: theme === "DARK" ? "#333" : "#fff",
  };

  return (
    <ThemeContext.Provider value={{ theme, themeStyle, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export default ThemeState;
```

Now, we add the following code to the `main.tsx` or `index.tsx` file:
```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App.tsx";
import "./index.css";
import ThemeState from "./Context/ThemeState.tsx";

ReactDOM.createRoot(document.getElementById("root") as HTMLElement).render(
  <React.StrictMode>
    <ThemeState>
      <App />
    </ThemeState>
  </React.StrictMode>
);
```

And then will write codes for our main app logic inside the `App.tsx` file:
```tsx
import { useContext } from "react";
import themeContext from "./Context/themeContext";

const App = () => {
  const { theme, themeStyle, setTheme } = useContext(themeContext);
  const handleToggleTheme = () => {
    if (theme === "DARK") {
      setTheme("LIGHT");
    } else {
      setTheme("DARK");
    }
  };

  return (
    <div style={themeStyle}>
      <button onClick={handleToggleTheme}>Toggle Theme</button>
      <h1>Current theme style is {theme}</h1>
      <p>This React app shows the application of createContext Hook</p>
    </div>
  );
};

export default App;
```

## [`useReducer`](https://react.dev/reference/react/useReducer) Hook
The`useReducer` Hook lets you add a reducer to your component.

```tsx
const [state, dispatch] = useReducer(reducer, initialArg, init?);
```

### Example
Example code for the `useReducer` Hook is given below:

```tsx
import { useReducer, useState } from "react";

type State = {
  count: number;
  text: string;
};

type Payload = {
  nextChange?: number;
  nextText?: string;
};

const enum REDUCER_ACTION_TYPE {
  INCREMENT,
  DECREMENT,
  NEW_TEXT,
}

type ReducerAction = {
  type: REDUCER_ACTION_TYPE;
  payload: Payload;
};

const reducer = (state: State, action: ReducerAction): State => {
  switch (action.type) {
    case REDUCER_ACTION_TYPE.DECREMENT:
      return {
        ...state,
        count: state.count - (action.payload.nextChange ?? 0),
      };
    case REDUCER_ACTION_TYPE.INCREMENT:
      return {
        ...state,
        count: state.count + (action.payload.nextChange ?? 0),
      };
    case REDUCER_ACTION_TYPE.NEW_TEXT:
      return { ...state, text: action.payload.nextText ?? "" };
    default:
      throw new Error();
  }
};

const initialState: State = { count: 0, text: "" };

const App = () => {
  // with optional initializer function
  const [state, dispatch] = useReducer(reducer, initialState, (state) => ({
    ...state,
    count: 1000,
  }));
  const [text, setText] = useState<string>("");

  const increment = () => {
    dispatch({
      type: REDUCER_ACTION_TYPE.INCREMENT,
      payload: { nextChange: 2 },
    });
  };
  const decrement = () => {
    dispatch({
      type: REDUCER_ACTION_TYPE.DECREMENT,
      payload: { nextChange: 2 },
    });
  };
  const handleInput = () => {
    dispatch({
      type: REDUCER_ACTION_TYPE.NEW_TEXT,
      payload: { nextText: text },
    });
    setText("");
  };

  return (
    <>
      <h1>
        {state.count} {state.text}
      </h1>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <br />
      <input
        type="text"
        value={text}
        onChange={(event) => setText(event.target.value)}
      />
      <button onClick={handleInput}>Change Name</button>
    </>
  );
};

export default App;
```

## [`useSyncExternalStore`](https://react.dev/reference/react/useSyncExternalStore) Hook

The `useSyncExternalStore` is a React Hook that lets you subscribe to an external store.

```tsx
const snapshot = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

### Example
Example code of `useSyncExternalStore` is given below:

Code for external store inside the file `store.ts`:
```tsx
type Listener<T> = (currentState: T) => unknown;

type Store<T> = {
  getState: () => T;
  setState: (newState: T) => void;
  subscribe: (listener: Listener<T>) => () => boolean;
};

const createStore = <T>(initialState: T): Store<T> => {
  let currentState = initialState;
  const listeners: Set<Listener<T>> = new Set();
  return {
    getState: () => currentState,
    setState: (newState: T) => {
      currentState = newState;
      listeners.forEach((listener) => listener(currentState));
    },
    subscribe: (listener) => {
      listeners.add(listener);

      return () => listeners.delete(listener);
    },
  };
};

export type State = {
  [index: string]: number;
};

const store: Store<State> = createStore<State>({
  value1: 0,
  value2: 0,
});

export default store;
```

Code for the main app logic inside the file `App.tsx` is:
```tsx
import { useSyncExternalStore } from "react";
import store, { State } from "./store/store";

const useStore = (selector: (state: State) => number) =>
  useSyncExternalStore(store.subscribe, () => selector(store.getState()));

const DisplayValue = ({ item }: { item: string }) => {
  return (
    <div>
      {item}: {useStore((state) => state[item])}
    </div>
  );
};

const IncrementValue = ({ item }: { item: keyof State }) => {
  return (
    <button
      onClick={() => {
        const state = store.getState();
        store.setState({
          ...state,
          [item]: state[item] + 1,
        });
      }}
    >
      Increment {item}
    </button>
  );
};

const App = () => {
  return (
    <div
      style={{
        display: "grid",
        gridTemplateColumns: "1fr 1fr",
        maxWidth: 600,
        gap: "1rem",
      }}
    >
      <IncrementValue item="value1" />
      <DisplayValue item="value1" />
      <IncrementValue item="value2" />
      <DisplayValue item="value2" />
    </div>
  );
};

export default App;
```

## [`useImperativeHandle`](https://react.dev/reference/react/useImperativeHandle)
The `useImperativeHandle` Hook lets you customize the handle exposed as a ref.

```tsx
useImperativeHandle(ref, createHandle, dependencies?);
```

### Example
We can forward the `ref` in React by using the `forwardRef`as follows:

Main application logic in the `App.tsx` file:
```tsx
import { useState, useRef } from "react";
import CustomInput from "./components/CustomInput";

const App = () => {
  const [value, setValue] = useState<string>("red");
  const inputRef = useRef<HTMLInputElement>(null);
  return (
    <>
      <CustomInput
        ref={inputRef}
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
      />
      <br />
      <button
        onClick={() => {
          if (!inputRef.current) return;
          inputRef.current.focus();
        }}
      >
        Focus
      </button>
    </>
  );
};

export default App;
```

Our custom input code in the `CustomInput.tsx` file:
```tsx
import { CSSProperties, ChangeEvent, forwardRef } from "react";

type Props = {
  type: string;
  value: string;
  onChange: (event: ChangeEvent<HTMLInputElement>) => void;
  style?: CSSProperties;
};

const CustomInput = forwardRef<HTMLInputElement, Props>(
  ({ style, ...props }, ref) => {
    return (
      <input
        ref={ref}
        {...props}
        style={{
          border: "none",
          backgroundColor: "lightpink",
          padding: ".25em",
          borderBottom: ".1em solid black",
          borderTopRightRadius: ".25em",
          borderTopLeftRadius: ".25em",
          ...style,
        }}
      />
    );
  }
);

export default CustomInput;
```

Using `useImperativeHandle` to change the behavior of the forwarded `ref` as following:

Changing behavior of forwarded ref inside the `CustomInput.tsx`, using the `useImperativeHandle` Hook:
```tsx
import {
  CSSProperties,
  ChangeEvent,
  forwardRef,
  useImperativeHandle,
  useRef,
} from "react";

export type CustomInputRefType = {
  alertValue: () => void;
};

type Props = {
  type: string;
  value: string;
  onChange: (event: ChangeEvent<HTMLInputElement>) => void;
  style?: CSSProperties;
};

const CustomInput = forwardRef<CustomInputRefType, Props>(
  ({ style, ...props }, ref) => {
    const inputRef = useRef<HTMLInputElement>(null);
    useImperativeHandle(
      ref,
      () => {
        return {
          alertValue: () => {
            console.log(props.value);
            inputRef.current?.focus();
          },
        };
      },
      [props.value]
    );

    return (
      <input
        ref={inputRef}
        {...props}
        style={{
          border: "none",
          backgroundColor: "lightpink",
          padding: ".25em",
          borderBottom: ".1em solid black",
          borderTopRightRadius: ".25em",
          borderTopLeftRadius: ".25em",
          ...style,
        }}
      />
    );
  }
);

export default CustomInput;
```

Contents of the `App.tsx` file after using the `useImperativeHandle` Hook:
```tsx
import { useState, useRef } from "react";
import CustomInput, { CustomInputRefType } from "./components/CustomInput";

const App = () => {
  const [value, setValue] = useState<string>("red");
  const inputRef = useRef<CustomInputRefType>(null);
  return (
    <>
      <CustomInput
        ref={inputRef}
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
      />
      <br />
      <button
        onClick={() => {
          if (!inputRef.current) return;
          inputRef.current.alertValue();
        }}
      >
        Focus
      </button>
    </>
  );
};

export default App;
```

## [`useTransition`](https://react.dev/reference/react/useTransition) Hook

The`useTransition` Hook that lets you update the state without blocking the UI.
```tsx
const [isPending, startTransition] = useTransition();
```

### Example
In the application code shown below, when we type inside of the text input, the typed characters are not shown immediately because of the long for-loop overhead.

```tsx
import { ChangeEvent, useEffect, useState } from "react";

const App = () => {
  const [input, setInput] = useState<string>("");
  const [list, setList] = useState<string[]>([]);

  useEffect(() => {
    const LIST_SIZE = 20_000;
    const l: string[] = [];

    for (let i = 0; i < LIST_SIZE; i++) {
      l.push(input);
    }

    setList(l);
  }, [input]);

  const handleInput = (event: ChangeEvent<HTMLInputElement>) => {
    setInput(event.target.value);
  };

  return (
    <>
      <input type="text" value={input} onChange={handleInput} />
      {list.map((item, index) => (
        <div key={index}>{item}</div>
      ))}
    </>
  );
};

export default App;
```

This application can be fixed using the `useTransition` Hook as shown below:
```tsx
import { ChangeEvent, useEffect, useState, useTransition } from "react";

const App = () => {
  const [input, setInput] = useState<string>("");
  const [list, setList] = useState<string[]>([]);
  const [isPending, startTransition] = useTransition();

  const LIST_SIZE = 20_000;

  useEffect(() => {
    startTransition(() => {
      const l: string[] = [];

      for (let i = 0; i < LIST_SIZE; i++) {
        l.push(input);
      }

      setList(l);
    });
  }, [input]);

  const handleInput = (event: ChangeEvent<HTMLInputElement>) => {
    setInput(event.target.value);
  };

  return (
    <>
      <input type="text" value={input} onChange={handleInput} />
      {isPending ? (
        <h1>Loading...</h1>
      ) : (
        list.map((item, index) => <div key={index}>{item}</div>)
      )}
    </>
  );
};

export default App;
```

## [`useDeferredValue`](https://react.dev/reference/react/useDeferredValue)
The `useDeferredValue` Hook that lets you defer updating a part of the UI.

```ts
const deferredValue = useDeferredValue(value);
```

### Example
In the application code shown below, when we type inside of the text input, the typed characters are not shown immediately because of the long for-loop overhead.

```tsx
import { ChangeEvent, useEffect, useState } from "react";

const App = () => {
  const [input, setInput] = useState<string>("");
  const [list, setList] = useState<string[]>([]);

  useEffect(() => {
    const LIST_SIZE = 20_000;
    const l: string[] = [];

    for (let i = 0; i < LIST_SIZE; i++) {
      l.push(input);
    }

    setList(l);
  }, [input]);

  const handleInput = (event: ChangeEvent<HTMLInputElement>) => {
    setInput(event.target.value);
  };

  return (
    <>
      <input type="text" value={input} onChange={handleInput} />
      {list.map((item, index) => (
        <div key={index}>{item}</div>
      ))}
    </>
  );
};

export default App;
```

This application can be fixed using the `useDeferredValue` Hook as shown below:
```tsx
import { ChangeEvent, useDeferredValue, useEffect, useState } from "react";

const App = () => {
  const [input, setInput] = useState<string>("");
  const [list, setList] = useState<string[]>([]);
  const deferredInput = useDeferredValue(input);

  const LIST_SIZE = 20_000;

  useEffect(() => {
    const l: string[] = [];

    for (let i = 0; i < LIST_SIZE; i++) {
      l.push(deferredInput);
    }

    setList(l);
  }, [deferredInput]);

  const handleInput = (event: ChangeEvent<HTMLInputElement>) => {
    setInput(event.target.value);
  };

  return (
    <>
      <input type="text" value={input} onChange={handleInput} />
      {list.map((item, index) => (
        <div key={index}>{item}</div>
      ))}
    </>
  );
};

export default App;
```

## [`useId`](https://react.dev/reference/react/useId)
The `useId` Hook for generating unique IDs that can be passed to accessibility attributes.

```tsx
const id = useId()
```

### Example
```tsx
import { useId } from "react";

const EmailInput = () => {
  const id = useId();
  return (
    <>
      <label htmlFor={id}>Email</label>
      <input type="email" id={id} />
    </>
  );
};

export default EmailInput;
```

```tsx
import EmailInput from "./components/EmailInput"

const App = () => {
  return (
    <>
      {/* Both of the EmailInput has different ids */}
      <EmailInput />
      <p>
        Lorem ipsum dolor sit amet, consectetur adipisicing elit. Officia
        aspernatur earum delectus quibusdam nesciunt aliquam dicta rerum
        facilis. Eaque illum asperiores explicabo, necessitatibus doloribus
        excepturi sapiente aliquam porro iusto cum.
      </p>
      <EmailInput />
    </>
  );
};

export default App;
```

## Building a custom hook called `useLocalStorage`

Our custom hook the `useLocalStorage` hook saves and retrieves value from local storage. The implementation and and example usage of this hook is given below:

```tsx
import { Dispatch, SetStateAction, useEffect, useState } from "react";

const getSavedValue = <T>(key: string, initialValue: T | (() => T)): T => {
  const savedValue: T | null = JSON.parse(localStorage.getItem(key) || "null");
  if (savedValue) return savedValue;

  if (initialValue instanceof Function) return initialValue();

  return initialValue;
};

const useLocalStorage = <T>(
  key: string,
  initialValue: T
): Readonly<[T, Dispatch<SetStateAction<T>>]> => {
  const [value, setValue] = useState<T>(() =>
    getSavedValue<T>(key, initialValue)
  );

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
};

export default useLocalStorage;
```

### Example
```tsx
import { useEffect, useState } from "react";
import useLocalStorage from "./hooks/useLocalStorage";

const App = () => {
  const [name, setName] = useState<string>("");
  const [saveName, setSaveName] = useLocalStorage<string>("name", name);

  useEffect(() => {
    setSaveName(name);
  }, [name, setSaveName]);

  return (
    <>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <h1>{saveName}</h1>
    </>
  );
};

export default App;
```

[`useDebugValue`](https://react.dev/reference/react/useDebugValue)

The `useDebugValue` Hook lets you add a label to a custom Hook in React DevTools.

```tsx
useDebugValue(value, format?);
```

Example of `useDebugValue` for our `useLocalStorage` Hook is given below:

### Example
```tsx
import {
  Dispatch,
  SetStateAction,
  useDebugValue,
  useEffect,
  useState,
} from "react";

const getSavedValue = <T>(key: string, initialValue: T | (() => T)): T => {
  const savedValue: T | null = JSON.parse(localStorage.getItem(key) || "null");
  if (savedValue) return savedValue;

  if (initialValue instanceof Function) return initialValue();

  return initialValue;
};

const useLocalStorage = <T>(
  key: string,
  initialValue: T
): Readonly<[T, Dispatch<SetStateAction<T>>]> => {
  const [value, setValue] = useState<T>(() =>
    getSavedValue<T>(key, initialValue)
  );

  // code: 1
  // useDebugValue(key);
  // useDebugValue(value);

  // code: 2
  // code 2 is same as code 1
  // useDebugValue([key, value]);

  // code: 3
  // for some value that takes time, it will only go through this when we'll open React developer,s tool
  useDebugValue(value, (v) => getValueSlowly(v));

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
};

const getValueSlowly = <T>(value: T) => {
  for (let i = 0; i < 3_000_000_000; i++) {}
  return `This is value ${value}`;
};

export default useLocalStorage;
```