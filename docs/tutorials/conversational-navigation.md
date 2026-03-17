---
title: Conversational Navigation
description: Learn how to integrate AXSDK to let users navigate complex websites through natural conversation.
---

# Conversational Navigation: Navigating Complex Sites with AXSDK

## Overview

Large, feature-rich websites — with sprawling menus, nested subpages, and multi-step flows — can quickly become a source of frustration. Users often know exactly where they want to go, but finding the right path through an unfamiliar UI takes time, patience, and more clicks than it should.

AXSDK eliminates that friction. Instead of forcing users to hunt through navigation structures, AXSDK lets them simply describe their intent in natural language — "Take me to the refund policy" or "Show me the pricing plans for enterprise" — and the SDK handles the rest. By connecting user intent directly to navigation logic, AXSDK turns complex site structures into something a user can move through in plain conversation.

This tutorial walks you through integrating AXSDK's conversational navigation capability into an existing website. You will learn how to register navigation functions with the `AXHandler`, map natural language intents to your site's routes, and surface the experience through AXSDK's built-in UI component.

## What You'll Learn

- How to set up AXSDK for navigation on a complex site
- How to handle user navigation intents with `AXHandler`
- How to map conversation to site routes using `AX_navigate`
- How to register navigation schemas in the AXSDK Admin Dashboard
- How to test and validate conversational navigation flows

## Prerequisites

Before starting this tutorial, make sure you have the following:

- **Node.js 18+** installed on your machine
- **An AXSDK account** — sign up at [https://axsdk.ai](https://axsdk.ai)
- **A Next.js project** — you can use your own or clone the example repository:
  [https://github.com/layorixinc/axsdk-examples/tree/main/packages/navigation](https://github.com/layorixinc/axsdk-examples/tree/main/packages/navigation)
- **An OpenRouter API key** — required for the BYOK (Bring Your Own Key) configuration in Step 2

## Step 1: Create an App on AXSDK

Before writing any code, you need to create an app on the AXSDK dashboard — this registers your project and gives you the credentials you will need to initialize the SDK.

1. Go to [https://axsdk.ai](https://axsdk.ai) and log in to your account.
2. Click your **user avatar** in the top-right corner — a dropdown menu will appear. Select **"App"** from the dropdown.
3. Click the **"Add App"** button. Fill in the following fields:
   - **Name** — a recognizable name for your app
   - **App ID** — a unique identifier for your app
   - **Description** — a brief description of what your app does

   Then click **"Create App"** to save.
4. Your newly created app will now appear in the left sidebar under the **Apps** navigation item.

With your app created, you are ready to move on to the setup and integration steps below.

## Step 2: Configure the App

### Get Your API Key

Before configuring the app settings, you must first generate an API key. This key will be used to authenticate AXSDK in your application code.

1. Navigate to your app's settings page. In the top-right corner of the **App Settings** panel, click the **"API Keys"** button to go to the key management page.
2. Click **"Create API Key"** to generate a new key.
3. **Copy and securely store your API key** — it will only be shown once.

Select the app you just created from the left sidebar to open its configuration panel. You will see four settings areas that need to be configured: **Sitemap**, **System Prompt**, **Tool Schema**, and **Domain Whitelist**. This step covers the **Sitemap** setting first.

### Sitemap

The Sitemap tells AXSDK what pages exist on your site and what they contain, enabling the SDK to understand navigation intent. Paste the following sitemap content into the Sitemap field, then click **"Save Sitemap"**.

!!! note
    This sitemap represents a demo site with a multi-level navigation structure, covering products, services, company information, and contact pages.

```markdown
# Navigation Demo Site
> AXSDK demo site showcasing a multi-level navigation structure with products, services, company information, and contact pages.

## Home
- [Home](/): The main landing page of the Navigation Demo Site.

## Products
Overview of all product offerings including software and hardware categories.
- [Products](/products): Browse all available products.
- [Software](/products/software): Explore the full range of software products.
- [Web Apps](/products/software/web-apps): Browser-based web application solutions.
- [Mobile Apps](/products/software/mobile-apps): Mobile applications for iOS and Android platforms.
- [Desktop Apps](/products/software/desktop-apps): Desktop applications for Windows, macOS, and Linux.
- [Hardware](/products/hardware): Explore the full range of hardware products.
- [Computers](/products/hardware/computers): Desktop and laptop computer systems.
- [Peripherals](/products/hardware/peripherals): Keyboards, mice, monitors, and other accessories.
- [Networking](/products/hardware/networking): Routers, switches, and networking equipment.

## Services
Professional services including consulting, support, and training.
- [Services](/services): Overview of all available services.
- [Consulting](/services/consulting): Expert consulting services for your business needs.
- [IT Consulting](/services/consulting/it-consulting): Technology strategy and IT infrastructure consulting.
- [Business Consulting](/services/consulting/business-consulting): Business process improvement and management consulting.
- [Strategy](/services/consulting/strategy): Long-term strategic planning and advisory services.
- [Support](/services/support): Comprehensive support services for customers.
- [Technical Support](/services/support/technical): Technical troubleshooting and issue resolution.
- [Customer Service](/services/support/customer-service): General customer service and inquiries.
- [Knowledge Base](/services/support/knowledge-base): Self-service documentation and guides.
- [Training](/services/training): Educational programs to develop your team's skills.
- [Online Courses](/services/training/online-courses): Self-paced online learning modules and video courses.
- [Workshops](/services/training/workshops): Hands-on instructor-led training workshops.
- [Certifications](/services/training/certifications): Professional certification programs and exams.

## About
Learn more about our company, career opportunities, and latest news.
- [About](/about): Overview of the company and its mission.
- [Company](/about/company): Detailed information about the organization.
- [History](/about/company/history): The founding story and milestones of the company.
- [Mission](/about/company/mission): Our core values, vision, and mission statement.
- [Team](/about/company/team): Meet the leadership and key team members.
- [Careers](/about/careers): Explore opportunities to join our team.
- [Open Positions](/about/careers/open-positions): Current job openings and available roles.
- [Benefits](/about/careers/benefits): Employee benefits, perks, and compensation details.
- [Culture](/about/careers/culture): Our workplace culture, values, and environment.
- [News](/about/news): Stay up to date with the latest company news.
- [Press Releases](/about/news/press-releases): Official press releases and announcements.
- [Blog](/about/news/blog): Articles, insights, and updates from our team.
- [Events](/about/news/events): Upcoming and past company events and conferences.

## Contact
Get in touch with us through the appropriate channel for your needs.
- [Contact](/contact): Overview of all contact options.
- [General Inquiries](/contact/general): For general questions and feedback.
- [Sales](/contact/sales): Connect with our sales team for pricing and demos.
- [Support](/contact/support): Reach our support team for technical or account help.
```

Once you have pasted the sitemap content, click **"Save Sitemap"** to save the configuration.

### System Prompt

The System Prompt gives AXSDK explicit instructions on how to handle navigation requests. The default system prompt works reasonably well out of the box, but providing a more explicit prompt leads to more predictable and accurate navigation behavior.

Paste the following content into the **System Prompt** field and click **"Save System Prompt"** to save.

```markdown
# Core Workflow
Whenever a user requests to view, visit, or find a specific page, you must strictly follow this sequence of actions:

1. **Consult the Sitemap:** Look up the requested page in the provided sitemap context to identify the exact, correct URL or link.
2. **Execute Navigation:** Once you have verified the URL from the sitemap, you must use the `AX_navigate` tool to navigate to that specific link.

# Rules and Constraints
- NEVER guess URLs. Always verify the link against the sitemap first.
- If the requested page does not exist in the sitemap, inform the user that the page cannot be found instead of attempting to use the `AX_navigate` tool with a hallucinated link.
- Only use the `AX_navigate` tool for routing the user.
```

This prompt does three key things:

- **Enforces a strict two-step workflow** — the AI must consult the sitemap before performing any navigation, ensuring routes are always grounded in the known site structure.
- **Prevents URL hallucination** — by requiring sitemap verification before acting, the AI cannot fabricate or guess links that do not exist.
- **Constrains the navigation mechanism** — `AX_navigate` is designated as the sole tool for routing the user, keeping navigation behavior consistent and auditable.

### Tool Schema

The Tool Schema defines the tools that the AXSDK agent can call. Two tools are defined for this tutorial:

- **`get_env`** — Called by the agent to check the user's current environment (e.g., what page they are currently on)
- **`navigate`** — Called by the agent when it has decided to navigate to a link defined in the sitemap

Paste the following JSON into the **Tool Schema** field:

```json
[
  {
    "name": "get_env",
    "description": "get current app environment",
    "parameters": {
      "properties": {}
    }
  },
  {
    "name": "navigate",
    "description": "navigate app screens with corresponding screen name and parameters.",
    "parameters": {
      "type": "object",
      "properties": {
        "link": {
          "type": "string",
          "description": "Link to navigate to"
        },
        "parameters": {
          "type": "object",
          "description": "Additional navigation options"
        }
      },
      "required": [
        "link"
      ]
    }
  }
]
```

After pasting, click **"Save Tool Schema"** to save the configuration.

Here is what each tool does:

- **`get_env`** — Takes no parameters. The agent calls this tool to retrieve context about the user's current state, such as the current URL or page they are viewing.
- **`navigate`** — Accepts the following parameters:
    - `link` (string, required) — The URL to navigate to.
    - `parameters` (object, optional) — Additional navigation options passed along with the navigation request.

### Domain Whitelist

The Domain Whitelist restricts which domains AXSDK is allowed to access. **If no domain is specified, AXSDK will not allow access to any domain** — so this field must be filled in for the integration to work. This is a security feature that ensures the SDK only operates on explicitly approved origins.

Since this tutorial uses a Next.js app that runs on port `3000` by default, add the following entry to the **Domain Whitelist** field:

```text
http://localhost:3000
```

After entering the domain, click **"Save Domain Whitelist"** to save the configuration.

!!! tip
    When deploying to production, replace `http://localhost:3000` with your actual production domain (e.g., `https://yourdomain.com`).

### BYOK (Bring Your Own Key)

The free tier of AXSDK requires users to supply their own LLM API key to handle AI requests — this is known as **BYOK (Bring Your Own Key)**. Without configuring this, the AXSDK agent will not be able to process any requests.

To configure BYOK:

1. In the left sidebar, navigate to **Settings** → **BYOK**.
2. Select a **Model** and enter the corresponding **API Key** for that model's provider.
3. Click **Save** to apply the settings.

At the time of writing, only **OpenRouter** is available as a provider in the free tier, and supported models are currently limited. More providers and models will be added in future updates.

!!! note
    BYOK is only required on the free tier. When AXSDK introduces paid plans, managed LLM access may be included without needing to supply your own key.

## Step 3: Integrate AXSDK into Your Next.js App

Now that the app is configured on the AXSDK dashboard, it's time to add AXSDK to the Next.js project.

### 3.1 — Install the AXSDK Packages

```bash
npm install zustand "@axsdk/core" "@axsdk/react"
```

If you are using a different package manager (e.g., `yarn`, `pnpm`), substitute the install command accordingly.

### 3.2 — Create the `AXProvider` Component

To make AXSDK easy to use across the React app, we wrap AXSDK initialization and the AXUI widget into a single `AXProvider` component.

At a high level, this component:

- Initializes AXSDK with the API key and App ID via `AXSDK.init()`
- Registers an `axHandler` callback that handles two commands:
    - `AX_get_env` — returns the user's current environment state (e.g., current URL) from a Zustand store
    - `AX_navigate` — uses Next.js `router.push()` to navigate to the specified link after a short delay
- Tracks the current page location and updates the environment store when the URL changes
- Renders the `<AXUI />` chat widget

Create a file named `AXProvider.tsx` and paste the following code:

```tsx
"use client"

import { useEffect } from 'react';
import { useRouter } from 'next/navigation';
import { AXSDK } from "@axsdk/core"
import { AXUI } from "@axsdk/react"
import "@axsdk/react/index.css"
import { create } from "zustand"

export const useEnvStore = create((set, get) => ({
  env: {},
  setEnv: (env: any) => set({ env }),
  getEnv: () => (get() as any).env,
  updateEnv: (newEnv: any) => set((state: any) => ({ env: { ...state.env, ...newEnv } }))
}))

export const useActionStore = create((set, get) => ({
  actions: {},
  setActions: (actions: any) => set({ actions }),
}))

export function AXProvider() {
  const router = useRouter()

  useEffect(() => {
    (useActionStore.getState() as any).setActions({ router })
  }, [router])

  useEffect(() => {
    AXSDK.init({
      apiKey: process.env.NEXT_PUBLIC_AXSDK_API_KEY,
      appId: process.env.NEXT_PUBLIC_AXSDK_APP_ID,
      axHandler: async (command: string, args: unknown) => {
        console.log(command, args);
        if(command == "AX_get_env") {
          return {
            status: "OK",
            data: (useEnvStore.getState() as any).getEnv()
          };
        }
        if(command == "AX_navigate") {
          setTimeout(() => {
            (useActionStore.getState() as any).actions.router.push(`${(args as any).link}`, undefined, {});
          }, 2000)
        }
        return { status: "OK" };
      },
    })
  }, []);

  useEffect(() => {
    console.log("location", global.window.location.href);
    (useEnvStore.getState() as any).updateEnv({ location: global.window.location.href })
  }, [global.window?.location.href])

  return (<AXUI></AXUI>)
}
```

Here is a breakdown of the key parts:

- **`useEnvStore`** — a Zustand store that holds the current environment state (e.g., `location`). The agent reads this when `AX_get_env` is called.
- **`useActionStore`** — a Zustand store that holds runtime actions like the `router` instance, so they can be accessed outside of React component scope.
- **`AXSDK.init()`** — initializes the SDK with the API key (`NEXT_PUBLIC_AXSDK_API_KEY`) and App ID (`NEXT_PUBLIC_AXSDK_APP_ID`) from environment variables.
- **`axHandler`** — the callback that bridges AXSDK agent commands to your app logic. Handles `AX_get_env` (returns current env) and `AX_navigate` (calls `router.push()` with a 2-second delay).
- **Location tracking** — the third `useEffect` watches `window.location.href` and updates the env store, keeping the agent aware of the user's current page.
- **`<AXUI />`** — renders the floating chat widget that users interact with.

!!! note
    `NEXT_PUBLIC_` prefix is required by Next.js to expose environment variables to the browser. Add `NEXT_PUBLIC_AXSDK_API_KEY` and `NEXT_PUBLIC_AXSDK_APP_ID` to your `.env.local` file.

## Step 4: Mount `AXProvider` in Your App

With the `AXProvider` component created, the next step is to mount it somewhere in the app where it will be active on every page. In this example, it is added to the root `layout.tsx` file so the AXUI chat widget is always visible and AXSDK runs globally across all routes.

```tsx title="layout.tsx"
import type { Metadata } from 'next';
import './globals.css';
import Navigation from './components/Navigation';
import { AXProvider } from "./AXProvider"

export const metadata: Metadata = {
  title: 'Navigation Example',
  description: 'A multi-level navigation example built with Next.js',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body style={{ margin: 0, padding: 0, fontFamily: 'system-ui, sans-serif' }}>
        <div style={{ display: 'flex', minHeight: '100vh' }}>
          <Navigation />
          <main style={{ flex: 1, padding: '24px', maxWidth: '900px' }}>
            {children}
          </main>
          <AXProvider/>
        </div>
      </body>
    </html>
  );
}
```

Here is what's happening in this file:

- **`AXProvider`** is imported from the `AXProvider.tsx` file created in the previous step.
- It is placed inside the root layout's `<body>`, alongside the `<Navigation />` sidebar and `<main>` content area.
- Mounting it here ensures the AXUI chat widget and AXSDK initialization run on every page of the app.
- The `<AXProvider />` renders the `<AXUI />` widget which appears as a floating chat button for the user to interact with.

!!! tip
    You can place `<AXProvider />` anywhere in the component tree, but mounting it in the root layout ensures it is always active. If you only need AXSDK on specific pages, mount it in those page components instead.

## Step 5: Set Up Environment Variables

Before running the app, the **API Key** and **App ID** obtained in Step 2 must be set as environment variables. Next.js reads these from a `.env` file at the root of the project.

Create a file named `.env` in the project root and add the following:

```bash
NEXT_PUBLIC_AXSDK_API_KEY=your_api_key_here
NEXT_PUBLIC_AXSDK_APP_ID=your_app_id_here
```

- Replace `your_api_key_here` with the **API Key** generated in Step 2.
- Replace `your_app_id_here` with the **App ID** defined when creating the app in Step 1.
- The `NEXT_PUBLIC_` prefix makes these variables accessible in the browser (client-side), which is required for `AXSDK.init()` to work.

!!! warning
    Never commit `.env` to version control. Add it to your `.gitignore` file to keep your credentials secure.

## Step 6: Test Conversational Navigation

The AXSDK integration is now complete. Start your Next.js development server and open the app in your browser.

```bash
npm run dev
```

Once the app is running, a floating **AX button** will appear in the **bottom-right corner** of the screen. Click it to open the AXSDK chat widget.

Try the following example queries to test navigation:

- `sitemap?` — asks the agent to describe the site structure
- `what services do you have?` — navigates or describes the **Services** section
- `careers` — navigates directly to the **Careers** page
- `events` — navigates to the **Events** page

The integration is now fully working. If the agent misunderstands a request, try **refining the System Prompt** in the AXSDK dashboard and test again.

**Important:** After updating any app configuration (**System Prompt**, **Sitemap**, **Tool Schema**, etc.), the conversation must be **restarted** for the new settings to take effect — use the **trash icon** in the bottom-left of the chat widget to clear the conversation.

!!! note
    The **trash icon** may be hidden behind the Next.js DevTools overlay — try moving the **DevTools** panel if you cannot see it.

!!! tip
    The **trash icon** clears the current conversation context, forcing the agent to reload the latest configuration. Always reset the conversation after making changes on the AXSDK dashboard.

## Conclusion

In this tutorial, you accomplished the following:

- Created and configured an AXSDK app on the dashboard, including the **Sitemap**, **System Prompt**, **Tool Schema**, **Domain Whitelist**, and **BYOK** settings
- Installed the AXSDK packages and built the `AXProvider` component
- Mounted the widget in a Next.js root layout using `layout.tsx`
- Tested conversational navigation using natural language queries

The complete source code for this example is available on GitHub:

**[https://github.com/layorixinc/axsdk-examples/tree/main/packages/navigation](https://github.com/layorixinc/axsdk-examples/tree/main/packages/navigation)**

Explore the full project to see the implementation in context. From here, you can extend the sitemap to cover additional pages, customize the system prompt to refine agent behavior, or bring the same conversational navigation pattern into more complex applications.

---

## A Note on Beta Status

AXSDK is currently in **beta**, which means you may encounter rough edges or unexpected behavior. If something isn't working as expected, start by double-checking your **app configuration** (Sitemap, System Prompt, Tool Schema, Domain Whitelist) and your **BYOK settings**.

Your feedback is invaluable in shaping the direction of AXSDK. If you run into issues or have suggestions, please reach out:

**[layorixinc@gmail.com](mailto:layorixinc@gmail.com)**
