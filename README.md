# React Hooks

## [`useState`](https://react.dev/reference/react/useState) Hook
`useState` Hook lets you add a state variable to your component.

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

`useEffect` Hook lets you synchronize a component with an external system.

```tsx
useEffect(setup, dependencies?)
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

`useRef` is a React Hook that lets you reference a value that’s not needed for rendering.

```tsx
const ref = useRef(initialValue)
```

### Example
The app below counts the number of times the app rendered using `useRef` Hook.

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

People mostly use `useRef` Hooks to reference the HTML elements inside of the `JSX/TSX`:

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
useLayoutEffect is a version of useEffect that fires before the browser repaints the screen.

```tsx
useLayoutEffect(setup, dependencies?)
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
`useMemo` Hook lets you cache the result of a calculation between re-renders.
```tsx
const cachedValue = useMemo(calculateValue, dependencies)
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

## [`useCallback`](https://react.dev/reference/react/useCallback) Hook
`useCallback` Hook lets you cache a function definition between re-renders.

```tsx
const cachedFn = useCallback(fn, dependencies)
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

`useContext` Hook lets you read and subscribe to context from your component.

```tsx
const value = useContext(SomeContext)
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

Contents of `themeContext.ts` file:
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

Contents of `ThemeState.tsx` file:
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

Now, we add following code to the `main.tsx` or `index.tsx` file:
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

And then will write codes for our main app logic inside `App.tsx` file:
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
`useReducer` Hook lets you add a reducer to your component.

```tsx
const [state, dispatch] = useReducer(reducer, initialArg, init?)
```

### Example
Example code for `useReducer` Hook is given below:

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