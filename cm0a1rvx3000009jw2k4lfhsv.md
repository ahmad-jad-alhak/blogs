---
title: "Improve SPFx Projects Using Custom Hooks for Enhanced Performance and Reusability"
datePublished: Sun Aug 25 2024 20:54:12 GMT+0000 (Coordinated Universal Time)
cuid: cm0a1rvx3000009jw2k4lfhsv
slug: improve-spfx-projects-using-custom-hooks-for-enhanced-performance-and-reusability
tags: reactjs, sharepoint, sharepoint-development, reacthooks, sharepoint-online, sharepoint-framework, html-frontend-developer-web-internet-javascript-programming, sharepointframework

---

React Hooks represent the modern way of writing React applications. By adopting Hooks in your SPFx projects, you're not only improving your current development workflow but also future-proofing your codebase. As the React ecosystem continues to evolve, more libraries and tools are being built with functional components and Hooks in mind, making it a smart investment in your skills and your projects in the long term.

In the context of SharePoint Framework (SPFx) projects, custom hooks offer even greater benefits by encapsulating reusable logic, reducing redundancy, and enhancing the maintainability of your codebase. In this blog, we’ll explore what custom hooks are, why they are so powerful in SPFx, and how you can start creating your own to simplify your SharePoint development.

## What Are Custom Hooks?

Custom hooks are functions that allow you to reuse stateful logic across multiple components. They start with the word “use” and can encapsulate logic that would otherwise be repeated throughout your application. By creating custom hooks, you can share this logic across various components, making your code more modular, maintainable, and easier to understand.

## Why Custom Hooks Matter in SPFx Development?

In SPFx, where you often need to interact with SharePoint data, manage user inputs, and handle side effects, custom hooks are particularly useful. Here’s why:

* **Enhanced Reusability with Custom Hooks:** One of the most powerful features of React Hooks is the ability to create custom Hooks. Custom Hooks allow you to encapsulate logic that can be reused across multiple components, promoting DRY (Don't Repeat Yourself) principles. In SPFx development, where you may have similar logic spread across different components, custom Hooks can significantly reduce code duplication and improve maintainability.
    
    ```typescript
    // Custom Hook for Fetching Data
    function useFetchData(url) {
      const [data, setData] = useState(null);
      const [loading, setLoading] = useState(true);
    
      useEffect(() => {
        fetch(url)
          .then(response => response.json())
          .then(data => {
            setData(data);
            setLoading(false);
          });
      }, [url]);
    
      return { data, loading };
    }
    
    // Using the Custom Hook in a Component
    function MyComponent() {
      const { data, loading } = useFetchData('https://api.example.com/data');
    
      if (loading) {
        return <div>Loading...</div>;
      }
    
      return <div>{data}</div>;
    }
    ```
    
* **Simplification:** By moving complex logic out of your components and into custom hooks, you simplify your components. This makes them easier to understand, maintain, and test. For example, the `useEffect` hook allows you to run side-effect logic in response to component lifecycle events, without the boilerplate code required in class components.
    
* **Maintainability:** When your logic is centralized in a custom hook, making updates is easier. A change in the hook will automatically apply wherever the hook is used, ensuring consistent behavior.
    
* **Performance Optimization:** Custom hooks can help you optimize performance by using React’s memoization features (useCallback, useMemo) to prevent unnecessary re-renders and expensive calculations.
    

## Converting Class-Based Components to Functional Components

Before we dive into creating custom hooks, it’s important to understand how to convert existing class-based components to functional components. By default, the Yeoman SharePoint generator creates a template with class-based components when you generate an SPFx project. This conversion is necessary to enable the use of custom hooks and to fully take advantage of React hooks in your SPFx projects.

### Example: Converting a Class-Based Component to a Functional Component

Let’s start with a simple class-based component that fetches items from a SharePoint list and displays them. Then, we’ll convert it to a functional component.

**Original Class-Based Component**

```typescript
import * as React from 'react';
import styles from './ReactCustomHooksPnP.module.scss';
import type { IReactCustomHooksPnPProps } from './IReactCustomHooksPnPProps';
import { escape } from '@microsoft/sp-lodash-subset';

export default class ReactCustomHooksPnP extends React.Component<IReactCustomHooksPnPProps, {}> {
  public render(): React.ReactElement<IReactCustomHooksPnPProps> {
    const {
      description,
      isDarkTheme,
      environmentMessage,
      hasTeamsContext,
      userDisplayName
    } = this.props;

    return (
      <section className={`${styles.reactCustomHooksPnP} ${hasTeamsContext ? styles.teams : ''}`}>
        <div className={styles.welcome}>
          <img alt="" src={isDarkTheme ? require('../assets/welcome-dark.png') : require('../assets/welcome-light.png')} className={styles.welcomeImage} />
          <h2>Well done, {escape(userDisplayName)}!</h2>
          <div>{environmentMessage}</div>
          <div>Web part property value: <strong>{escape(description)}</strong></div>
        </div>
        <div>
          <h3>Welcome to SharePoint Framework!</h3>
          <p>
            The SharePoint Framework (SPFx) is an extensibility model for Microsoft Viva, Microsoft Teams, and SharePoint. It&#39;s the easiest way to extend Microsoft 365 with automatic Single Sign On, automatic hosting, and industry-standard tooling.
          </p>
          <h4>Learn more about SPFx development:</h4>
          <ul className={styles.links}>
            <li><a href="https://aka.ms/spfx" target="_blank" rel="noreferrer">SharePoint Framework Overview</a></li>
            <li><a href="https://aka.ms/spfx-yeoman-graph" target="_blank" rel="noreferrer">Use Microsoft Graph in your solution</a></li>
            <li><a href="https://aka.ms/spfx-yeoman-teams" target="_blank" rel="noreferrer">Build for Microsoft Teams using SharePoint Framework</a></li>
            <li><a href="https://aka.ms/spfx-yeoman-viva" target="_blank" rel="noreferrer">Build for Microsoft Viva Connections using SharePoint Framework</a></li>
            <li><a href="https://aka.ms/spfx-yeoman-store" target="_blank" rel="noreferrer">Publish SharePoint Framework applications to the marketplace</a></li>
            <li><a href="https://aka.ms/spfx-yeoman-api" target="_blank" rel="noreferrer">SharePoint Framework API reference</a></li>
            <li><a href="https://aka.ms/m365pnp" target="_blank" rel="noreferrer">Microsoft 365 Developer Community</a></li>
          </ul>
        </div>
      </section>
    );
  }
}
```

**Converted Functional Component**

Here’s how you can convert the above class-based component into a functional component using React hooks:

```typescript
import * as React from 'react';
import styles from './ReactCustomHooksPnP.module.scss';
import type { IReactCustomHooksPnPProps } from './IReactCustomHooksPnPProps';
import { escape } from '@microsoft/sp-lodash-subset';

const ReactCustomHooksPnP: React.FC<IReactCustomHooksPnPProps> = (props) => {
  const {
    description,
    isDarkTheme,
    environmentMessage,
    hasTeamsContext,
    userDisplayName
  } = props;

  return (
    <section className={`${styles.reactCustomHooksPnP} ${hasTeamsContext ? styles.teams : ''}`}>
      <div className={styles.welcome}>
        <img alt="" src={isDarkTheme ? require('../assets/welcome-dark.png') : require('../assets/welcome-light.png')} className={styles.welcomeImage} />
        <h2>Well done, {escape(userDisplayName)}!</h2>
        <div>{environmentMessage}</div>
        <div>Web part property value: <strong>{escape(description)}</strong></div>
      </div>
      <div>
        <h3>Welcome to SharePoint Framework!</h3>
        <p>
          The SharePoint Framework (SPFx) is an extensibility model for Microsoft Viva, Microsoft Teams, and SharePoint. It&#39;s the easiest way to extend Microsoft 365 with automatic Single Sign On, automatic hosting, and industry-standard tooling.
        </p>
        <h4>Learn more about SPFx development:</h4>
        <ul className={styles.links}>
          <li><a href="https://aka.ms/spfx" target="_blank" rel="noreferrer">SharePoint Framework Overview</a></li>
          <li><a href="https://aka.ms/spfx-yeoman-graph" target="_blank" rel="noreferrer">Use Microsoft Graph in your solution</a></li>
          <li><a href="https://aka.ms/spfx-yeoman-teams" target="_blank" rel="noreferrer">Build for Microsoft Teams using SharePoint Framework</a></li>
          <li><a href="https://aka.ms/spfx-yeoman-viva" target="_blank" rel="noreferrer">Build for Microsoft Viva Connections using SharePoint Framework</a></li>
          <li><a href="https://aka.ms/spfx-yeoman-store" target="_blank" rel="noreferrer">Publish SharePoint Framework applications to the marketplace</a></li>
          <li><a href="https://aka.ms/spfx-yeoman-api" target="_blank" rel="noreferrer">SharePoint Framework API reference</a></li>
          <li><a href="https://aka.ms/m365pnp" target="_blank" rel="noreferrer">Microsoft 365 Developer Community</a></li>
        </ul>
      </div>
    </section>
  );
};

export default ReactCustomHooksPnP;
```

## Creating Your First Custom Hook in SPFx

Let’s start with a simple example. Suppose you need to fetch items from a SharePoint list in multiple components. Instead of writing the same data-fetching logic in each component, you can create a custom hook to handle this task.

### **Example: Creating a** `useSPListItems` **Hook**

This custom hook will fetch items from a SharePoint list and manage the loading and error states.

**Installing Required Packages**

Before using this custom hook with logging, ensure you have the necessary npm packages installed. You can install them by running:

```bash
npm install @pnp/sp @pnp/logging --save
```

For more details, you can refer to the [PnPJS documentation](https://pnp.github.io/pnpjs/).

```typescript
import { useState, useEffect, useCallback } from 'react';
import { SPFI, spfi, SPFx } from '@pnp/sp';
import '@pnp/sp/webs';
import '@pnp/sp/lists';
import '@pnp/sp/items';
import { Logger, LogLevel } from '@pnp/logging';
import { WebPartContext } from '@microsoft/sp-webpart-base';

interface UseSPListItemsResult<T> {
  items: T[];
  loading: boolean;
  error: Error | null;
  refetch: () => void;
}

const useSPListItems = <T = any>(context: WebPartContext, listName: string): UseSPListItemsResult<T> => {
  const [items, setItems] = useState<T[]>([]);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<Error | null>(null);

  const fetchItems = useCallback(async () => {
    Logger.activeLogLevel = LogLevel.Info;
    Logger.write(`Fetching items from list: ${listName}`, LogLevel.Info);

    try {
      const sp: SPFI = spfi().using(SPFx(context));
      const listItems: T[] = await sp.web.lists.getByTitle(listName).items();
      setItems(listItems);
      Logger.write(`Fetched ${listItems.length} items from list: ${listName}`, LogLevel.Info);
    } catch (err) {
      const errorMessage = err instanceof Error ? err.message : 'Unknown error occurred';
      setError(new Error(errorMessage));
      Logger.write(`Error fetching items from list: ${listName}. Error: ${errorMessage}`, LogLevel.Error);
    } finally {
      setLoading(false);
    }
  }, [context, listName]);

  useEffect(() => {
    fetchItems();
  }, [fetchItems]);

  return { items, loading, error, refetch: fetchItems };
};

export default useSPListItems;
```

## How This Custom Hook Works

* **State Management:** The hook manages three pieces of state—items, loading, and error. These states are essential for tracking the fetched data, the loading process, and any errors that occur during the fetch operation.
    
* **Fetching Logic:** The `fetchItems` function handles the logic for fetching data from the SharePoint list. This function is memoized using `useCallback` to prevent unnecessary re-creations.
    
* **Side Effects Management:** The `useEffect` hook automatically triggers the data fetch when the component using this hook mounts or when the dependencies (context and listName) change.
    
* **Reusability and Consistency:** This hook can be used in any component that needs to fetch items from a SharePoint list, ensuring that the fetching logic is consistent and centralized.
    

## Using the Custom Hook in a Component

Now that we have our `useSPListItems` hook, let’s see how it can be used in a component:

```typescript
import React from 'react';
import useSPListItems from '../hooks/useSPListItems';
import { WebPartContext } from '@microsoft/sp-webpart-base';

interface IMyFunctionalComponentProps {
  context: WebPartContext;
  listName: string;
}

const MyFunctionalComponent: React.FC<IMyFunctionalComponentProps> = ({ context, listName }) => {
  const { items, loading, error, refetch } = useSPListItems(context, listName);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <ul>
      {items.map((item: any) => (
        <li key={item.Id}>{item.Title}</li>
      ))}
    </ul>
  );
};

export default MyFunctionalComponent;
```

## Benefits of Using the Custom Hook in SPFx Components

* **Consistency:** By using the `useSPListItems` hook, the data-fetching logic is consistent across all components that need to fetch SharePoint list items.
    
* **Code Reusability:** The hook allows you to reuse the fetching logic across different components, reducing redundancy and potential errors.
    
* **Simplified Components:** The component logic is now focused on rendering the UI, while the complex fetching logic is encapsulated in the hook. This separation of concerns makes your components easier to understand and maintain.
    

## Conclusion

Custom hooks are a game-changer in SPFx development, allowing you to encapsulate complex logic into reusable functions that can be shared across components. By leveraging custom hooks, you can make your code more maintainable, reduce redundancy, and enhance the overall performance of your applications.

As you continue to develop SPFx solutions, I encourage you to experiment with custom hooks in your projects. Whether you’re handling data fetching, form management, or optimizing performance, custom hooks provide a powerful way to keep your components clean, focused, and efficient.

## Further Reading

* [Set up your SharePoint Framework development environment](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-development-environment)
    
* [PnPJS documentation](https://pnp.github.io/pnpjs/)
    
* [React Hooks Documentation](https://react.dev/reference/react/hooks)