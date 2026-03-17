# Eliminate Complex UI, Introduce Agentic UX

Modern applications are plagued by feature bloat. As features are added, menus get deeper, navigation becomes convoluted, and users get tired. Competitor apps force clicks; your app should execute intentions.

With **AXSDK**, users shouldn't get lost in menus. By applying **Agentic UX**, you empower your users to interact with your application using natural language. Just declare your existing APIs in `AXHandler`, and our advanced AI engine translates natural language into immediate, precise actions. See the difference an agent makes: transform your app from a static interface into an intelligent assistant.

## SDK Architecture & Packages

Currently, AXSDK is available exclusively for **TypeScript** environments. We provide a highly modular architecture split into logic and UI:

*   📦 **`@axsdk/core`**: The headless core package. It manages the AI state, network communication with the AX Engine, and the `AXHandler` execution logic. It has zero UI dependencies.
*   ⚛️ **`@axsdk/react`**: The dedicated React UI library. It provides beautiful, drop-in components (like chat interfaces and voice buttons) that seamlessly bind to the core engine.

> 🚧 **Roadmap:** SDKs for other web UI frameworks (Vue, Svelte) and Mobile Native platforms (iOS, Android, React Native, Flutter) are currently actively under development. Stay tuned!

## The Two-Step Magic

AXSDK separates the intelligence from the execution, keeping your codebase clean and secure.

1. **The Brain (AX Console):** In our management dashboard, you define the **System Prompt**, upload your **Sitemap**, and register the **Function Schemas**. This teaches the AI exactly what your app is capable of and how it should behave.
2. **The Hands (`AXHandler`):** Inside your code using `@axsdk/core`, you simply provide a mapping array. When the AI decides an action is needed, it pings your `AXHandler`, which safely executes your existing local functions.

## The Paradigm Shift

| Traditional GUI | Agentic UX with AXSDK |
| :--- | :--- |
| User clicks 5 times to find "Order History" | User says "Where is my order?" |
| App forces user to filter categories manually | User types "Find cheap summer t-shirts" |
| **Cognitive Load:** $O(n)$ based on menu depth | **Cognitive Load:** $O(1)$ instant execution |

Ready to upgrade your UX?

[Get Started with the Quickstart 🚀](getting-started/quickstart.md)

## Resources

* [🌐 Website](resources/website.md)
* [🌐 Live Demo](resources/live-demo.md)
