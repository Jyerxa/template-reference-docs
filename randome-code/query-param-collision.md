# A thought about query parameter collision in micro-frontends

## Overview

This project considers how to use a custom React hook (`useMyParams`) and a context provider (`AppParamProvider`) to manage URL parameters with a unique application name prefix.

## Usage

### Setting Up the Provider

Wrap your main application component with the `AppParamProvider` and provide a unique application name.

```typescript
import Main from "./components/Main/Main";
import { AppParamProvider } from "./providers/AppParamProvider";

export default function Root(props) {
  return (
    <AppParamProvider appName="uniqueAppName">
      <Main />
    </AppParamProvider>
  );
}
````

Using the Hook
Use the `useMyParams` hook in your component to access and manipulate URL parameters.
    
```typescript
import { useMyParams } from "../../hooks/useMyParams";

const Main = () => {
  const { params, deleteParam, addParam } = useMyParams();

  const addParamClick = () => {
    const key = (document.querySelector("input[placeholder='Key']") as HTMLInputElement).value;
    const value = (document.querySelector("input[placeholder='Value']") as HTMLInputElement).value;
    addParam(key, value);
  };

  return (
    <div>
      <h3>Params</h3>
      <table>
        <thead>
          <tr>
            <th>Key</th>
            <th>Value</th>
          </tr>
        </thead>
        <tbody>
          {params.length > 0 &&
            params.map(([key, value]) => (
              <tr key={key}>
                <td>{key}</td>
                <td>{value}</td>
                <td>
                  <button onClick={() => deleteParam(key)}>Delete</button>
                </td>
              </tr>
            ))}
        </tbody>
      </table>
      <div>Add Param</div>
      <input type="text" placeholder="Key" />
      <input type="text" placeholder="Value" />
      <button onClick={addParamClick}>Add</button>
    </div>
  );
};

export default Main;
```


### Source Code

`AppParamProvider.tsx`
```typescript  
import React, { createContext, useContext, ReactNode } from "react";

const AppParamContext = createContext<string | undefined>(undefined);

export const AppParamProvider = ({ appName, children }: { appName: string, children: ReactNode }) => {
  return (
    <AppParamContext.Provider value={appName}>
      {children}
    </AppParamContext.Provider>
  );
};

export const useAppParam = () => {
  const context = useContext(AppParamContext);
  if (context === undefined) {
    throw new Error("useAppParam must be used within an AppParamProvider");
  }
  return context;
};
```

    `useMyParams.ts`
```typescript
import { useEffect, useState } from "react";
import { useAppParam } from "../providers/AppParamProvider";

export const useMyParams = () => {
  const appName = useAppParam();
  const [params, setParams] = useState<[string, string][]>([]);

  useEffect(() => {
    const searchParams = new URLSearchParams(window.location.search);
    const newParams: [string, string][] = [];
    searchParams.forEach((value, key) => {
      if (key.startsWith(`${appName}:`)) {
        newParams.push([key, value]);
      }
    });
    setParams(newParams);
  }, [params]);

  const deleteParam = (key: string) => {
    const searchParams = new URLSearchParams(window.location.search);
    searchParams.delete(key);
    window.history.pushState({}, "", `${window.location.pathname}?${searchParams}`);
    setParams(Array.from(searchParams.entries()).filter(([k]) => k.startsWith(`${appName}:`)));
  };

  const addParam = (key: string, value: string) => {
    const searchParams = new URLSearchParams(window.location.search);
    searchParams.set(`${appName}:${key}`, value);
    window.history.pushState({}, "", `${window.location.pathname}?${searchParams}`);
    setParams(Array.from(searchParams.entries()).filter(([k]) => k.startsWith(`${appName}:`)));
  };

  return { params, deleteParam, addParam, appName };
};
```

`main.tsx`

```typescript
import { useMyParams } from "../../hooks/useMyParams";

const Main = () => {
  const { params, deleteParam, addParam } = useMyParams();

  const addParamClick = () => {
    const key = (document.querySelector("input[placeholder='Key']") as HTMLInputElement).value;
    const value = (document.querySelector("input[placeholder='Value']") as HTMLInputElement).value;
    addParam(key, value);
  };

  return (
    <div>
      <h3>Params</h3>
      <table>
        <thead>
          <tr>
            <th>Key</th>
            <th>Value</th>
          </tr>
        </thead>
        <tbody>
          {params.length > 0 &&
            params.map(([key, value]) => (
              <tr key={key}>
                <td>{key}</td>
                <td>{value}</td>
                <td>
                  <button onClick={() => deleteParam(key)}>Delete</button>
                </td>
              </tr>
            ))}
        </tbody>
      </table>
      <div>Add Param</div>
      <input type="text" placeholder="Key" />
      <input type="text" placeholder="Value" />
      <button onClick={addParamClick}>Add</button>
    </div>
  );
};

export default Main;
```

`root.component.tsx`    
```typescript
import Main from "./components/Main/Main";
import { AppParamProvider } from "./providers/AppParamProvider";

export default function Root(props) {
  return (
    <AppParamProvider appName="uniqueAppName">
      <Main />
    </AppParamProvider>
  );
}
```
