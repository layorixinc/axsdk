# Implementation Guide

Implementing Agentic UX with AXSDK is a straightforward two-step process.

## Step 1: Admin Page Configuration

**Note: This is a crucial step before writing any code.**

Before diving into the codebase, you must configure your agent's behavior in the AXSDK Admin Dashboard. This involves:

* **System Prompts:** Define the persona, boundaries, and general instructions for your AI agent.
* **Sitemap:** Provide the agent with an understanding of your application's structure and navigation paths.
* **AXHandler Function Schemas:** Register the schemas for the functions your agent is allowed to call. This tells the AI engine exactly what capabilities are available and what parameters they require.

## Step 2: Code Implementation

Once your admin configuration is complete, the next step is to connect your application's logic to the agent. You do this by declaring your existing APIs in the `AXHandler`.

Here is a standard implementation example:

```typescript
const AX_FUNCTIONS = {
  AX_get_env,
  AX_navigate,
  AX_get_product_types,
  AX_get_product_categories,
  AX_search_products,
  AX_show_product,
  AX_add_to_cart,
  AX_show_cart,
  AX_checkout,
  AX_order,
  AX_get_order_history,
  AX_search_faq,
} as const;

const AX_PROXY = new Proxy(AX_FUNCTIONS, {
  get(target, command: string) {
    return target[command as keyof typeof target] ?? (() => ({
      status: "ERROR",
      message: `Unknown command: ${command}`,
    }));
  },
});

export async function AXHandler(command: string, args: Record<string, unknown>) {
  console.log(`axHandler: ${command}:`, args);
  const result = await AX_PROXY[command as keyof typeof AX_FUNCTIONS](args);
  console.log(`axHandler: ${command}:`, result);
  return result;
}
```

By mapping your internal functions to the `AXHandler`, the AI engine can seamlessly invoke them based on user intent, bypassing the need for complex UI navigation.

### Creating an AXProvider for React

In React applications, your `AXHandler` might need to access internal state, other providers, or stores. To achieve this, developers may need to write an `AXProvider` for `AXHandler`.

```tsx
import { AXSDK } from '@axsdk/core';
import React, { useEffect } from 'react';

const AXContext = React.createContext<any>(null)

export const AXProvider = (props) => {
  const { me } = useUser()
  const router = useRouter()
  const { openModal, closeModal } = useModalAction();
  useEffect(() => {
    AXSDK.init({
      apiKey: "YOUR_AXSDK_API_KEY",
      appId: "YOUR_APP_ID",
      axHandler: AXHandler
    })
  }, []);
...
  return (
    <AXContext.Provider value={{ AXSDK, useEnvStore, useActionStore }}>
      {props.children}
    </AXContext.Provider>
  );
}
```

## Step 3: Mount the UI (@axsdk/react)

Once your handler and provider are ready, bind them to your application state using the React provider and components.

```tsx
import { AXUI } from '@axsdk/react';
import { AXProvider } from './axHandler';
import '@axsdk/react/index.css';

export default function App() {
  return (
    <main>
      <AXProvider>
        <YourAppContent />
      </AXProvider>
      {/* Drop-in the floating Agentic UX interface */}
      <AXUI />
    </main>
  );
}
```

### How It Comes Together
1. The user inputs their intention in natural language via the `AXUI` component.
2. The `@axsdk/core` processes this input with the backend AI Agent.
3. The `@axsdk/core` then calls the `AXHandler` according to the registered `AXHandler schemas` (e.g., triggering a command like `"AX_add_to_cart"`).
4. Your native code executes safely via the Proxy. Because it is integrated with `AXProvider`, it has full access to your React state, hooks, and stores, allowing it to update the UI instantly.

