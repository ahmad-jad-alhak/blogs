---
title: "Managing Complex State in SPFx Projects Using the useReducer Hook: A Step-by-Step Guide"
datePublished: Thu Jan 16 2025 15:00:13 GMT+0000 (Coordinated Universal Time)
cuid: cm5zgjbwk000709l7h9bzc1pj
slug: managing-complex-state-in-spfx-projects-using-the-usereducer-hook-a-step-by-step-guide
tags: reactjs, sharepoint, spfx, sharepoint-online, usereducer-hook, sharepointframework

---

State management is essential for creating dynamic and interactive components in SharePoint Framework (SPFx) solutions. While `useState` is a popular choice for managing state in React-based SPFx web parts, `useReducer` is better when the state logic becomes more complex. This article demonstrates the advantages of `useReducer` in SPFx solutions using a counter example, highlighting its power and flexibility.

### **Why State Management Matters in SPFx**

Imagine you’re building a web part that fetches data from multiple SharePoint lists. Managing the loading states, errors, and fetched data can quickly become messy, especially as your logic expands. Without a centralized approach, it’s easy to lose track of updates, causing bugs and inconsistencies. This is where `useReducer` shines—it centralizes state transitions and simplifies managing complex workflows.

## What is `useReducer` Hook?

The `useReducer` hook is a React hook that lets you manage state with a reducer function. This function decides how the state should change in response to specific actions. This method is particularly helpful in SPFx solutions where state transitions are complex or need a centralized approach.

For more details on `useReducer`, check out the official [React documentation.](https://react.dev/reference/react/useReducer)

### Syntax:

```typescript
const [state, dispatch] = useReducer(reducer, initialState);
```

* `reducer`: A function that takes the current state and an action, and returns the new state.
    
* `initialState`: The initial value of the state.
    
* `dispatch`: A function to send actions to the reducer.
    

## Benefits of Using `useReducer` Hook:

1. **Centralized State Management:** The reducer function centralizes all state updates, making the logic easier to understand and debug. This is especially useful when dealing with multiple state transitions.
    
2. **Shared Logic**: It allows for the reuse of state management logic across different components, which is beneficial when multiple components need to follow the same state management patterns.
    
3. **Scalability for Complex State**: `useReducer` is well-suited for managing complex state logic that involves multiple actions or state transitions. It allows for easy extension of state logic without cluttering the component with multiple `useState` calls.
    
4. **Predictable State Updates**: `useReducer` enforces immutability and ensures that state updates are predictable and follow the defined reducer logic, reducing the likelihood of bugs in complex workflows.
    
5. **Improved Readability and Debugging**: By using action types, state transitions become explicit, which enhances code readability. It also simplifies debugging, as you can log actions and state changes within the reducer.
    

# Comparing Both useState and useReducer Approaches:

To understand the benefits of `useReducer`, let’s build a simple counter web part using SPFx. We’ll compare this approach to a `useState` implementation to highlight why `useReducer` is a better choice for certain scenarios.

These benefits make `useReducer` a powerful tool for managing state in applications where state logic is complex or needs to be shared across components.

## **Counter Example**

### Using `useState` Approach

```typescript
import React, { useState } from 'react';

const CounterWithUseState: React.FC = () => {
  // `count` holds the current count value, and `setCount` is the function to update it.
  const [count, setCount] = useState(0);

  // `isEven` determines whether the current count is even or odd.
  // `setIsEven` updates this value based on the count.
  const [isEven, setIsEven] = useState(true);
    
  // Function to increment the count
  const increment = () => {
    const newCount = count + 1; // Increment the count by 1
    setCount(newCount); // Update the `count` state
    setIsEven(newCount % 2 === 0); // Update the `isEven` state based on the new count
  };

  // Function to decrement the count
  const decrement = () => {
    const newCount = count - 1; // Decrement the count by 1
    setCount(newCount); // Update the `count` state
    setIsEven(newCount % 2 === 0); // Update the `isEven` state based on the new count
  };

  // Function to reset the count and parity
  const reset = () => {
    setCount(0); // Reset the count to 0
    setIsEven(true); // Reset `isEven` to true since 0 is even
  };

  return (
    <div>
      {/* Display the current count */}
      <h1>Count: {count}</h1>

      {/* Display whether the count is even or odd */}
      <p>{isEven ? 'Even' : 'Odd'}</p>

      {/* Button to increment the count */}
      <button onClick={increment}>Increment</button>

      {/* Button to decrement the count */}
      <button onClick={decrement}>Decrement</button>

      {/* Button to reset the count */}
      <button onClick={reset}>Reset</button>
    </div>
  );
};

export default CounterWithUseState;
```

### Challenges with the `useState` Approach

1. **Multiple pieces of related state**  
    We have both `count` and `isEven` in separate hooks. Whenever the count changes, we have to manually update `isEven`. This can quickly become error-prone if we keep adding more related states.
    
2. **Adding new actions**  
    If you want to add new functionality (e.g., "double" the count), you’ll need additional logic inside each piece of state to keep them in sync. The logic can become scattered across multiple setter calls.
    

## **Using** `useReducer` Approach

Using `useReducer` allows you to centralize all the logic for updating state in a single reducer function. This helps keep your components clean, and it makes your state transitions easier to understand and extend.

### `counterReducer.ts`

```typescript
// reducers/counterReducer.ts

// Define the structure of the state
export interface CounterState {
  count: number;
  isEven: boolean;
}

// Define the structure of the actions
export interface CounterAction {
  type: 'INCREMENT' | 'DECREMENT' | 'RESET' | 'DOUBLE';
}

// Define the initial state
export const initialState: CounterState = {
  count: 0,
  isEven: true,
};

// Reducer function to manage state transitions
export const counterReducer = (state: CounterState, action: CounterAction): CounterState => {
  switch (action.type) {
    case 'INCREMENT': {
      const newCount = state.count + 1;
      return {
        count: newCount,
        isEven: newCount % 2 === 0,
      };
    }
    case 'DECREMENT': {
      const newCount = state.count - 1;
      return {
        count: newCount,
        isEven: newCount % 2 === 0,
      };
    }
    case 'RESET': {
      return initialState;
    }
    case 'DOUBLE': {
      const newCount = state.count * 2;
      return {
        count: newCount,
        isEven: newCount % 2 === 0,
      };
    }
    default:
      return state; // Return unchanged state for unrecognized actions
  }
};
```

### `CounterWithUseReducer.tsx`

```typescript
import React, { useReducer } from 'react';
import { counterReducer, initialState } from '../reducers/counterReducer';

const CounterWithUseReducer: React.FC = () => {
  // Initialize the state and dispatch function using useReducer
  const [state, dispatch] = useReducer(counterReducer, initialState);

  return (
    <div>
      <h1>Count: {state.count}</h1>
      <p>{state.isEven ? 'Even' : 'Odd'}</p>

      {/* Button to increment the count */}
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>Increment</button>

      {/* Button to decrement the count */}
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>Decrement</button>

      {/* Button to reset the count */}
      <button onClick={() => dispatch({ type: 'RESET' })}>Reset</button>

      {/* Button to double the count */}
      <button onClick={() => dispatch({ type: 'DOUBLE' })}>Double</button>
    </div>
  );
};

export default CounterWithUseReducer;
```

### Benefits of the `useReducer` Approach

1. **Centralized State Updates**  
    With a reducer, all logic for updating `count` and `isEven` is encapsulated in one place. This makes it easier to reason about how the state changes on each action.
    
2. **Easier to Add New Actions**  
    To add a new action, you simply extend the action type and handle it in the reducer. There’s no need to sprinkle additional `setState` calls in different places.
    
3. **Improved Readability**  
    Having a single reducer function that describes all possible state changes keeps your component simpler and more predictable.
    
4. **Scales Better for Complex State**  
    As your application grows, a reducer-based approach often becomes more maintainable than using multiple `useState` hooks for closely related pieces of state.
    

# Comparing Both Approaches `useState` and `useReducer` in SPFx Projects with PnP.js

When building **SharePoint Framework (SPFx)** web parts, you often need to manage **complex UI states**—like **loading indicators**, **error handling**, or multiple list fetches. While **PnP.js** simplifies calls to the SharePoint REST API, you still need to **organize** the resulting data and **manage** loading/error states effectively.

In SPFx projects, where components can grow quickly in complexity, `useReducer` offers a more structured way to manage state transitions, keeping components clean and logic straightforward. Let’s dive into the differences between state management using `useState` and `useReducer` to understand when each approach works best.

## Managing State with `useState`

#### **Key Characteristics of** `useState`:

* Suitable for **simple, independent states** (e.g., a single toggle, counter, or flag).
    
* Requires separate `useState` calls for each piece of state.
    
* Works best for components with **minimal state transitions**.
    

#### **Example: Using** `useState` for Fetching Items

Below is an example of using `useState` to fetch and display SharePoint list items:

```typescript
import * as React from 'react';
import styles from './ReactUseReducerHookBasic.module.scss';
import { IReactUseReducerHookBasicProps } from './IReactUseReducerHookBasicProps';
import { initialState, listReducer } from '../reducers/listReducer';
import { spfi, SPFx } from '@pnp/sp';
import '@pnp/sp/webs';
import '@pnp/sp/lists';
import '@pnp/sp/items';

const ReactUseReducerHookBasic: React.FC<IReactUseReducerHookBasicProps> = ({ listName }) => {
  const [items, setItems] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const fetchItems = async () => {
    setLoading(true);
    setError(null);

    try {
      const fetchedItems = await sp.web.lists.getByTitle(listName).items();
      setItems(fetchedItems);
    } catch (err) {
      setError('Failed to fetch items');
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchItems();
  }, []);

  return (
    <div>
      {loading && <p>Loading...</p>}
      {error && <p style={{ color: 'red' }}>{error}</p>}
      <ul>
        {items.map((item) => (
          <li key={item.Id}>{item.Title}</li>
        ))}
      </ul>
    </div>
  );
};

export default ReactUseReducerHookBasic;
```

#### **Challenges with** `useState`:

* Managing multiple, related states (e.g., `loading`, `error`, `items`) can become **tedious** and error-prone.
    
* State transitions are scattered across multiple setter calls, making logic harder to follow.
    
* Adding new state-related actions (e.g., pagination or retry logic) increases complexity.
    

### **Managing State with** `useReducer`

### **Key Characteristics of** `useReducer`:

* Suitable for **complex state logic** where actions affect multiple state variables.
    
* Centralizes state updates in a single reducer function, making transitions easier to understand.
    
* Scales well for components with **multiple actions or related states**.
    

#### **Example: Using** `useReducer` for Fetching Items

Here’s how the same functionality can be implemented with `useReducer`:

```typescript
import * as React from 'react';
import styles from './ReactUseReducerHookBasic.module.scss';
import { IReactUseReducerHookBasicProps } from './IReactUseReducerHookBasicProps';
import { initialState, listReducer } from '../reducers/listReducer';
import { spfi, SPFx } from '@pnp/sp';
import '@pnp/sp/webs';
import '@pnp/sp/lists';
import '@pnp/sp/items';

// Define action types
const FETCH_START = 'FETCH_START';
const FETCH_SUCCESS = 'FETCH_SUCCESS';
const FETCH_ERROR = 'FETCH_ERROR';

// Define the initial state
const initialState = {
  items: [],
  loading: false,
  error: null,
};

// Define the reducer
const listReducer = (state, action) => {
  switch (action.type) {
    case FETCH_START:
      return { ...state, loading: true, error: null };
    case FETCH_SUCCESS:
      return { ...state, loading: false, items: action.payload };
    case FETCH_ERROR:
      return { ...state, loading: false, error: action.payload };
    default:
      return state;
  }
};

const ReactUseReducerHookBasic: React.FC<IReactUseReducerHookBasicProps> = ({ listName }) => {
  const [state, dispatch] = useReducer(listReducer, initialState);

  const fetchItems = async () => {
    dispatch({ type: FETCH_START });

    try {
      const fetchedItems = await sp.web.lists.getByTitle(listName).items();
      dispatch({ type: FETCH_SUCCESS, payload: fetchedItems });
    } catch (err) {
      dispatch({ type: FETCH_ERROR, payload: 'Failed to fetch items' });
    }
  };

  useEffect(() => {
    fetchItems();
  }, []);

  return (
    <div>
      {state.loading && <p>Loading...</p>}
      {state.error && <p style={{ color: 'red' }}>{state.error}</p>}
      <ul>
        {state.items.map((item) => (
          <li key={item.Id}>{item.Title}</li>
        ))}
      </ul>
    </div>
  );
};

export default ReactUseReducerHookBasic;
```

Below is an example using `useReducer` with PnP.js to fetch items in an SPFx context. All state transitions are handled in one place (the **reducer**), making it easier to **modify** and **debug**.

## Step-by-Step Guide to Implementing `useReducer` with PnPjs and Creating the Folder Structure

This guide will walk you through the process of implementing a `useReducer`\-based state management system and organizing your project with a clean, scalable folder structure.

### **Step 1: Set Up SPFx Web Part**

1. **Open a Terminal or Command Prompt** Navigate to the folder where you want to create your project.
    
2. **Run the Yeoman SharePoint Generator**
    
    ```bash
    yo @microsoft/sharepoint
    ```
    
3. **Answer the Prompts:**
    
    * **Solution Name:** (e.g., `ReactUseReducerHookBasic`).
        
    * **Baseline Packages:** Select **SharePoint Online only (latest)**.
        
    * **Folder Location:** Select **Use the current folder**.
        
    * **Component Type:** Choose **WebPart**.
        
    * **WebPart Name:** (e.g., `ReactUseReducerHookBasic`).
        
    * **Framework:** Choose **React**.
        
4. **Navigate to the Project Folder:**
    
    ```bash
    cd ReactUseReducerHookBasic
    ```
    
5. **Install PnP.js Libraries:**
    
    ```bash
    npm install @pnp/sp @pnp/logging @pnp/common @pnp/odata
    ```
    
6. **Start the Local Development Server:**
    
    ```bash
    gulp serve
    ```
    
    This will open the **SharePoint Workbench** in your browser, where you can preview your web part.
    

### **Step 2: Implement the Reducer Logic**

#### **Folder Structure**

Create Create the following folders and files: Organize your project to maintain clean separation of concerns:

```bash
ReactUseReducerHookBasic/
├── src/
│   ├── webparts/
│   │   └── ReactUseReducerHookBasic/
│   │       ├── components/                 # UI Components
│   │       │   ├── ReactUseReducerHookBasic.tsx  # Main React Component
│   │       │   └── ReactUseReducerHookBasic.module.scss  # Component Styles
│   │       ├── state/                      # State management logic
│   │       │   ├── actions/                # Action types and creators
│   │       │   │   └── listActionsTypes.ts # Action types for list fetching
│   │       │   ├── reducers/               # Reducer logic and initial states
│   │       │   │   ├── listReducer.ts      # Reducer for managing list state
│   │       │   │   ├── initialStates.ts    # Initial state for reducers
│   │       │   └── index.ts                # Re-export reducers and actions
│   │       ├── ReactUseReducerHookBasicWebPart.ts  # WebPart entry file
│   │       └── index.ts                    # WebPart index file
```

1. #### **Define Action Types**
    
    Create `src/webparts//state/actions/listActionsTypes.ts` to define action types and their structure:
    
2. ```typescript
        //  src/webparts/ReactUseReducerHookBasic/state/actions/listActionsTypes.ts
        
        // Action types for list state management
        export const FETCH_START = 'FETCH_START';
        export const FETCH_SUCCESS = 'FETCH_SUCCESS';
        export const FETCH_ERROR = 'FETCH_ERROR';
        
        // Action type definitions
        export type ListAction =
          | { type: typeof FETCH_START }
          | { type: typeof FETCH_SUCCESS; payload: any[] }
          | { type: typeof FETCH_ERROR; payload: string };
    ```
    
3. **Create the Reducer**
    
    In `src/state/reducers/listReducer.ts`, implement the reducer logic:
    
    ```typescript
    //  src/webparts/ReactUseReducerHookBasic/state/reducers/listReducer.ts
    
    import { ListAction, FETCH_START, FETCH_SUCCESS, FETCH_ERROR } from '../actions/listActionsTypes';
    
    export interface ListState {
      items: any[];
      loading: boolean;
      error: string | null;
    }
    
    export const initialState: ListState = {
      items: [],
      loading: false,
      error: null,
    };
    
    export const listReducer = (state: ListState, action: ListAction): ListState => {
      switch (action.type) {
        case FETCH_START:
          return { ...state, loading: true, error: null };
        case FETCH_SUCCESS:
          return { ...state, loading: false, items: action.payload };
        case FETCH_ERROR:
          return { ...state, loading: false, error: action.payload };
        default:
          return state;
      }
    };
    ```
    
4. **Re-export State Files**
    
    In `src/state/index.ts`, re-export all reducers and actions for better imports:
    
    ```typescript
    // src/webparts/ReactUseReducerHookBasic/state/inedx.ts
    
    export * from './actions/listActionsTypes';
    export * from './reducers/listReducer';
    ```
    
    ### **Step 3: Use the Reducer in the Component**
    
    In `src/webparts/ReactUseReducerHookBasic/components/ReactUseReducerHookBasic.tsx`, integrate the reducer and implement the fetching logic:
    
    ```typescript
    // src/webparts/ReactUseReducerHookBasic/components/ReactUseReducerHookBasic.tsx
    
    import * as React from 'react';
    import styles from './ReactUseReducerHookBasic.module.scss';
    import { IReactUseReducerHookBasicProps } from './IReactUseReducerHookBasicProps';
    import { initialState, listReducer } from '../reducers/listReducer';
    import { spfi, SPFx } from '@pnp/sp';
    import '@pnp/sp/webs';
    import '@pnp/sp/lists';
    import '@pnp/sp/items';
    
    const ReactUseReducerHookBasic: React.FC<IReactUseReducerHookBasicProps> = ({ listName }) => {
      // Initialize our state and dispatch from the reducer
      const [state, dispatch] = useReducer(listReducer, initialState);
    
      /**
       * Asynchronous function to fetch items from the specified SharePoint list.
       * Dispatches actions to manage loading, success, and error states.
       */
      const fetchItems = async () => {
        // Indicate that a fetch request is starting
        dispatch({ type: 'FETCH_START' });
    
        try {
          // Fetch items from the SharePoint list using PnP.js
          const items = await sp.web.lists.getByTitle(listName).items();
          // On success, dispatch the fetched items to be stored in state
          dispatch({ type: 'FETCH_SUCCESS', payload: items });
        } catch (err) {
          // On error, store an error message
          dispatch({ type: 'FETCH_ERROR', payload: 'Failed to fetch list items' });
        }
      };
    
      /**
       * Use useEffect to fetch items on initial render (component mount).
       * This ensures that as soon as the component loads, data retrieval begins.
       */
      useEffect(() => {
        fetchItems();
      }, []);
    
      return (
        <div>
          {/* Show a loading indicator while items are being fetched */}
          {state.loading && <p>Loading...</p>}
    
          {/* Display any error messages in red */}
          {state.error && <p style={{ color: 'red' }}>{state.error}</p>}
    
          {/* Render the fetched items as a list */}
          <ul>
            {state.items.map((item) => (
              <li key={item.Id}>{item.Title}</li>
            ))}
          </ul>
    
          {/* If there's an error, provide a retry button */}
          {state.error && <button onClick={fetchItems}>Retry</button>}
        </div>
      );
    };
    
    export default ReactUseReducerHookBasic;
    ```
    
    ### **How It Works**
    
5. **Reducer & Initial State**
    
    * The component imports `listReducer` and `initialState` from your `listReducer` file. These define how your state transitions happen (e.g., `FETCH_START`, `FETCH_SUCCESS`, `FETCH_ERROR`) and what the initial data, loading, and error values should be.
        
6. **Dispatching Actions**
    
    * Before making the fetch call, the component dispatches `{ type: 'FETCH_START' }` to set `loading: true`.
        
    * On success, it dispatches `{ type: 'FETCH_SUCCESS', payload: items }` to store the items and set `loading: false`.
        
    * On error, it dispatches `{ type: 'FETCH_ERROR', payload: 'Error message' }`, which sets `loading: false` and updates the `error` field.
        
7. **useEffect**
    
    * The component calls `fetchItems()` once after it mounts, kicking off the data load immediately.
        

You can enhance the solution by adding custom hooks for improved reusability, as well as dedicated services and utility functions to streamline API calls and other shared logic.

```bash

src/
├── components/             # UI Components
│   ├── ReactUseReducerHookBasicExample.tsx   # The component for fetching and displaying items
│   ├── ....  # other default files
├── state/                  # State management logic
│   ├── actions/            # Action types and creators
│   │   ├── listActionsTypes.ts   # Action types for list fetching
│   ├── reducers/           # Reducer logic and initial states
│   │   ├── listReducer.ts        # Reducer for managing list state
│   │   ├── initialStates.ts     # Initial state for reducers
│   └── index.ts            # Re-export reducers and actions
├── services/               # API or utility services
│   ├── spService.ts        # SPFx or PnP.js services for SharePoint API
│   └── index.ts
├── hooks/                  # Custom hooks
│   ├── useFetchListItems    # Custom hook for fetching SharePoint list items
│   └── index.ts            # Re-export all hooks
├── utils/                  # General utility functions
│   ├── logger.ts           # Logging utility
│   ├── helpers.ts          # Helper functions
│   └── index.ts
```

## When to Use `useReducer` Hook in SPFx Solutions

In **SPFx** projects using **PnP.js**, deciding between `useState` and `useReducer` often comes down to the complexity of your data-fetching logic:

### `useState` Approach

* **Use** `useState` if your code is relatively **simple** and you only need a few pieces of state.
    
* **Great for simple scenarios** or small components where you have only a few states (`loading`, `error`, `data`).
    
* **Less setup complexity**, since you can directly call `setLoading(true)`, then `setItems(...)`, etc.
    
* **Becomes harder to scale** if you introduce more states or transitions (e.g., pagination, multiple fetch calls).
    

### `useReducer` Approach

* **Use** `useReducer` when you need **more structure**, have **multiple transitions**, or want a **centralized** place to manage complex state logic.
    
* **Centralizes state transitions** in a single reducer function, which improves maintainability.
    
* **State transitions are complex:** For example, managing multiple form fields or implementing workflows.
    
* **State updates depend on previous state:** Such as toggling components or navigating multi-step processes.
    
* **Shared logic:** If multiple components need to follow the same state management logic.
    
* **Scalability is a priority:** For web parts with features that will grow over time.
    
* **Scales better for complex logic** (e.g., multiple fetch scenarios, merging data from several lists, advanced error handling).
    
* **Clearer action semantics**: dispatch typed actions—such as `FETCH_START`, `FETCH_SUCCESS`, `FETCH_ERROR`—making the code more self-documenting.
    

Both methods are valid. Pick the one that **best fits** your web part’s complexity and the user experience you want to deliver.

### **Helpful Resources and Getting Started Guides**

To help you dive deeper into **SPFx**, **React-based Quick Views**, and **Adaptive Cards**, here are some useful links and resources:

* [**SPFx 1.20 Release Notes**](https://learn.microsoft.com/en-us/sharepoint/dev/spfx/release-1.20-release-notes): A detailed overview of all the new features and fixes in SPFx 1.20.
    
* [**Getting Started with SPFx**](https://learn.microsoft.com/en-us/sharepoint/dev/spfx/set-up-your-development-environment): Step-by-step guide to set up your development environment for SharePoint Framework.
    
* [**Using React with SPFx**](https://learn.microsoft.com/en-us/sharepoint/dev/spfx/web-parts/get-started/using-react): A guide that explains how to build SPFx web parts using React.
    
* [React useReducer Documentation:](https://react.dev/reference/react/useReducer) Official React Documentation provides an in-depth explanation of the `useReducer` hook, including examples and best practices.
    
* [**Fluent UI for SPFx**](https://developer.microsoft.com/en-us/fluentui#/get-started/web): Learn how to use Fluent UI (formerly Office UI Fabric) to create modern and accessible web experiences in SPFx.
    
* [**SASS in SPFx**](https://learn.microsoft.com/en-us/sharepoint/dev/spfx/web-parts/get-started/build-a-hello-world-web-part-using-sass): Learn how to apply SASS for custom styling in SPFx projects.
    
* [**SPFx GitHub Repository**](https://github.com/SharePoint/sp-dev-docs): The official GitHub repo for SPFx documentation and code samples.
    
* [**SPFx Community Samples**](https://github.com/pnp/sp-dev-fx-webparts): Explore a wide range of community-contributed SPFx samples for inspiration.