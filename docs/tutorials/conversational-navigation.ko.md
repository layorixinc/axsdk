---
title: 대화형 내비게이션
description: AXSDK를 통합하여 사용자가 자연스러운 대화로 복잡한 웹사이트를 탐색할 수 있는 방법을 알아보세요.
---

# 대화형 내비게이션: AXSDK로 복잡한 사이트 탐색하기

## 개요

방대한 메뉴, 중첩된 하위 페이지, 다단계 플로우를 가진 대형 기능 중심 웹사이트는 사용자에게 금세 답답함을 안겨줄 수 있습니다. 사용자는 원하는 목적지를 정확히 알고 있지만, 낯선 UI에서 올바른 경로를 찾는 데는 시간과 인내심, 그리고 과도한 클릭이 필요합니다.

AXSDK는 이러한 불편함을 없애줍니다. 사용자가 내비게이션 구조를 일일이 탐색하도록 강요하는 대신, AXSDK를 사용하면 "환불 정책 페이지로 데려다줘" 또는 "엔터프라이즈 요금제를 보여줘"처럼 자연어로 의도를 표현하기만 하면 SDK가 나머지를 처리합니다. 사용자 의도를 내비게이션 로직에 직접 연결함으로써, AXSDK는 복잡한 사이트 구조를 일상적인 대화로 자유롭게 이동할 수 있는 공간으로 바꿔줍니다.

이 튜토리얼에서는 기존 웹사이트에 AXSDK의 대화형 내비게이션 기능을 통합하는 과정을 안내합니다. `AXHandler`에 내비게이션 함수를 등록하고, 자연어 의도를 사이트 라우트에 매핑하며, AXSDK의 내장 UI 컴포넌트를 통해 사용자 경험을 제공하는 방법을 배우게 됩니다.

## 학습 내용

- 복잡한 사이트에서 내비게이션을 위한 AXSDK 설정 방법
- `AXHandler`로 사용자 내비게이션 의도를 처리하는 방법
- `AX_navigate`를 사용하여 대화를 사이트 라우트에 매핑하는 방법
- AXSDK 관리자 대시보드에서 내비게이션 스키마를 등록하는 방법
- 대화형 내비게이션 플로우를 테스트하고 검증하는 방법

## 사전 요구 사항

이 튜토리얼을 시작하기 전에 다음 항목이 준비되어 있는지 확인하세요:

- 로컬 머신에 **Node.js 18+** 설치
- **AXSDK 계정** — [https://axsdk.ai](https://axsdk.ai)에서 가입
- **Next.js 프로젝트** — 기존 프로젝트를 사용하거나 예제 저장소를 클론할 수 있습니다:
  [https://github.com/layorixinc/axsdk-examples/tree/main/packages/navigation](https://github.com/layorixinc/axsdk-examples/tree/main/packages/navigation)
- **OpenRouter API 키** — Step 2의 BYOK(Bring Your Own Key) 설정에 필요합니다

## Step 1: AXSDK에서 앱 생성

코드를 작성하기 전에, AXSDK 대시보드에서 앱을 생성해야 합니다. 이 작업은 프로젝트를 등록하고 SDK를 초기화하는 데 필요한 인증 정보를 제공합니다.

1. [https://axsdk.ai](https://axsdk.ai)로 이동하여 계정에 로그인합니다.
2. 오른쪽 상단의 **사용자 아바타**를 클릭하면 드롭다운 메뉴가 나타납니다. 드롭다운에서 **"App"**을 선택합니다.
3. **"Add App"** 버튼을 클릭합니다. 다음 항목을 입력합니다:
   - **Name** — 앱을 식별하기 쉬운 이름
   - **App ID** — 앱의 고유 식별자
   - **Description** — 앱의 기능에 대한 간단한 설명

   그런 다음 **"Create App"**을 클릭하여 저장합니다.
4. 새로 만든 앱이 왼쪽 사이드바의 **Apps** 내비게이션 항목 아래에 나타납니다.

앱이 생성되면 아래의 설정 및 통합 단계로 넘어갈 준비가 된 것입니다.

## Step 2: 앱 설정

### API 키 발급

앱 설정을 구성하기 전에 먼저 API 키를 생성해야 합니다. 이 키는 애플리케이션 코드에서 AXSDK를 인증하는 데 사용됩니다.

1. 앱의 설정 페이지로 이동합니다. **App Settings** 패널의 오른쪽 상단에서 **"API Keys"** 버튼을 클릭하여 키 관리 페이지로 이동합니다.
2. **"Create API Key"**를 클릭하여 새 키를 생성합니다.
3. **API 키를 복사하여 안전한 곳에 보관하세요** — 키는 한 번만 표시됩니다.

왼쪽 사이드바에서 방금 만든 앱을 선택하면 설정 패널이 열립니다. 구성해야 할 설정 영역이 네 가지 있습니다: **Sitemap**, **System Prompt**, **Tool Schema**, **Domain Whitelist**. 이 단계에서는 먼저 **Sitemap** 설정을 다룹니다.

### Sitemap

Sitemap은 사이트에 어떤 페이지가 존재하고 어떤 내용을 담고 있는지를 AXSDK에 알려주어, SDK가 내비게이션 의도를 이해할 수 있도록 합니다. 아래의 사이트맵 내용을 Sitemap 필드에 붙여넣고 **"Save Sitemap"**을 클릭합니다.

!!! note
    이 사이트맵은 제품, 서비스, 회사 정보, 연락처 페이지를 포함한 다단계 내비게이션 구조를 갖춘 데모 사이트를 나타냅니다.

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

사이트맵 내용을 붙여넣은 후 **"Save Sitemap"**을 클릭하여 설정을 저장합니다.

### System Prompt

System Prompt는 내비게이션 요청을 처리하는 방식에 대한 명시적인 지침을 AXSDK에 제공합니다. 기본 시스템 프롬프트도 기본적으로 잘 작동하지만, 더 명확한 프롬프트를 제공하면 보다 예측 가능하고 정확한 내비게이션 동작을 기대할 수 있습니다.

아래 내용을 **System Prompt** 필드에 붙여넣고 **"Save System Prompt"**를 클릭하여 저장합니다.

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

이 프롬프트는 세 가지 핵심 역할을 합니다:

- **엄격한 2단계 워크플로우 적용** — AI는 내비게이션을 실행하기 전에 반드시 사이트맵을 먼저 조회해야 하므로, 라우트가 항상 알려진 사이트 구조에 기반하게 됩니다.
- **URL 할루시네이션 방지** — 실행 전 사이트맵 검증을 요구함으로써 AI가 존재하지 않는 링크를 임의로 만들거나 추측하는 것을 막습니다.
- **내비게이션 메커니즘 제한** — `AX_navigate`가 사용자를 라우팅하는 유일한 도구로 지정되어, 내비게이션 동작이 일관되고 추적 가능하게 유지됩니다.

### Tool Schema

Tool Schema는 AXSDK 에이전트가 호출할 수 있는 도구를 정의합니다. 이 튜토리얼에서는 두 가지 도구가 정의됩니다:

- **`get_env`** — 에이전트가 사용자의 현재 환경(예: 현재 보고 있는 페이지)을 확인하기 위해 호출합니다
- **`navigate`** — 에이전트가 사이트맵에 정의된 링크로 내비게이션하기로 결정했을 때 호출합니다

아래 JSON을 **Tool Schema** 필드에 붙여넣습니다:

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

붙여넣은 후 **"Save Tool Schema"**를 클릭하여 설정을 저장합니다.

각 도구의 역할은 다음과 같습니다:

- **`get_env`** — 파라미터 없음. 에이전트가 현재 URL이나 보고 있는 페이지 등 사용자의 현재 상태에 대한 컨텍스트를 가져오기 위해 호출합니다.
- **`navigate`** — 다음 파라미터를 받습니다:
    - `link` (string, 필수) — 이동할 URL.
    - `parameters` (object, 선택) — 내비게이션 요청과 함께 전달되는 추가 내비게이션 옵션.

### Domain Whitelist

Domain Whitelist는 AXSDK가 접근할 수 있는 도메인을 제한합니다. **도메인이 지정되지 않으면 AXSDK는 어떤 도메인에도 접근을 허용하지 않습니다** — 따라서 통합이 작동하려면 이 필드를 반드시 입력해야 합니다. 이는 SDK가 명시적으로 승인된 출처에서만 작동하도록 보장하는 보안 기능입니다.

이 튜토리얼에서는 기본적으로 포트 `3000`에서 실행되는 Next.js 앱을 사용하므로, **Domain Whitelist** 필드에 다음 항목을 추가합니다:

```text
http://localhost:3000
```

도메인을 입력한 후 **"Save Domain Whitelist"**를 클릭하여 설정을 저장합니다.

!!! tip
    프로덕션에 배포할 때는 `http://localhost:3000`을 실제 프로덕션 도메인(예: `https://yourdomain.com`)으로 교체하세요.

### BYOK (Bring Your Own Key)

AXSDK 무료 티어에서는 AI 요청을 처리하기 위해 사용자가 자신의 LLM API 키를 직접 제공해야 합니다 — 이를 **BYOK(Bring Your Own Key)**라고 합니다. 이 설정 없이는 AXSDK 에이전트가 어떤 요청도 처리할 수 없습니다.

BYOK를 설정하려면:

1. 왼쪽 사이드바에서 **Settings** → **BYOK**로 이동합니다.
2. **Model**을 선택하고 해당 모델 제공자의 **API Key**를 입력합니다.
3. **Save**를 클릭하여 설정을 적용합니다.

현재 무료 티어에서는 **OpenRouter**만 제공자로 사용할 수 있으며, 지원 모델도 제한적입니다. 향후 업데이트에서 더 많은 제공자와 모델이 추가될 예정입니다.

!!! note
    BYOK는 무료 티어에서만 필요합니다. AXSDK에 유료 플랜이 도입되면 자체 키 없이도 관리형 LLM 접근이 포함될 수 있습니다.

## Step 3: Next.js 앱에 AXSDK 통합

AXSDK 대시보드에서 앱 설정이 완료되었으니, 이제 Next.js 프로젝트에 AXSDK를 추가할 차례입니다.

### 3.1 — AXSDK 패키지 설치

```bash
npm install zustand "@axsdk/core" "@axsdk/react"
```

다른 패키지 매니저(예: `yarn`, `pnpm`)를 사용하는 경우, 설치 명령어를 그에 맞게 변경하세요.

### 3.2 — `AXProvider` 컴포넌트 생성

React 앱 전체에서 AXSDK를 쉽게 사용하기 위해, AXSDK 초기화와 AXUI 위젯을 하나의 `AXProvider` 컴포넌트로 래핑합니다.

이 컴포넌트가 하는 일을 요약하면 다음과 같습니다:

- `AXSDK.init()`을 통해 API 키와 App ID로 AXSDK를 초기화합니다
- 두 가지 명령을 처리하는 `axHandler` 콜백을 등록합니다:
    - `AX_get_env` — Zustand 스토어에서 사용자의 현재 환경 상태(예: 현재 URL)를 반환합니다
    - `AX_navigate` — 짧은 지연 후 Next.js `router.push()`를 사용하여 지정된 링크로 이동합니다
- 현재 페이지 위치를 추적하고 URL이 변경될 때 환경 스토어를 업데이트합니다
- `<AXUI />` 채팅 위젯을 렌더링합니다

`AXProvider.tsx` 파일을 만들고 다음 코드를 붙여넣습니다:

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

주요 구성 요소에 대한 설명은 다음과 같습니다:

- **`useEnvStore`** — 현재 환경 상태(예: `location`)를 보관하는 Zustand 스토어. `AX_get_env`가 호출될 때 에이전트가 이 값을 읽습니다.
- **`useActionStore`** — `router` 인스턴스와 같은 런타임 액션을 보관하는 Zustand 스토어로, React 컴포넌트 스코프 밖에서도 접근할 수 있습니다.
- **`AXSDK.init()`** — 환경 변수에서 가져온 API 키(`NEXT_PUBLIC_AXSDK_API_KEY`)와 App ID(`NEXT_PUBLIC_AXSDK_APP_ID`)로 SDK를 초기화합니다.
- **`axHandler`** — AXSDK 에이전트 명령을 앱 로직에 연결하는 콜백. `AX_get_env`(현재 환경 반환)와 `AX_navigate`(2초 지연 후 `router.push()` 호출)를 처리합니다.
- **위치 추적** — 세 번째 `useEffect`가 `window.location.href`를 감시하고 환경 스토어를 업데이트하여, 에이전트가 사용자의 현재 페이지를 항상 파악할 수 있도록 합니다.
- **`<AXUI />`** — 사용자가 상호작용하는 플로팅 채팅 위젯을 렌더링합니다.

!!! note
    `NEXT_PUBLIC_` 접두사는 Next.js가 환경 변수를 브라우저에 노출하기 위해 필요합니다. `NEXT_PUBLIC_AXSDK_API_KEY`와 `NEXT_PUBLIC_AXSDK_APP_ID`를 `.env.local` 파일에 추가하세요.

## Step 4: 앱에 `AXProvider` 마운트

`AXProvider` 컴포넌트를 만들었다면, 이제 모든 페이지에서 활성화될 수 있도록 앱의 적절한 위치에 마운트해야 합니다. 이 예제에서는 루트 `layout.tsx` 파일에 추가하여 AXUI 채팅 위젯이 항상 보이고 AXSDK가 모든 라우트에서 전역으로 실행되도록 합니다.

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

이 파일에서 일어나는 일을 설명합니다:

- **`AXProvider`**는 이전 단계에서 만든 `AXProvider.tsx` 파일에서 임포트됩니다.
- 루트 레이아웃의 `<body>` 안에, `<Navigation />` 사이드바와 `<main>` 콘텐츠 영역 옆에 배치됩니다.
- 여기에 마운트하면 앱의 모든 페이지에서 AXUI 채팅 위젯과 AXSDK 초기화가 실행됩니다.
- `<AXProvider />`는 사용자가 상호작용하는 플로팅 채팅 버튼으로 표시되는 `<AXUI />` 위젯을 렌더링합니다.

!!! tip
    `<AXProvider />`는 컴포넌트 트리 어디에나 배치할 수 있지만, 루트 레이아웃에 마운트하면 항상 활성 상태가 보장됩니다. 특정 페이지에서만 AXSDK가 필요하다면, 해당 페이지 컴포넌트에 마운트하세요.

## Step 5: 환경 변수 설정

앱을 실행하기 전에, Step 2에서 획득한 **API 키**와 **App ID**를 환경 변수로 설정해야 합니다. Next.js는 프로젝트 루트의 `.env` 파일에서 이 값들을 읽습니다.

프로젝트 루트에 `.env` 파일을 만들고 다음 내용을 추가합니다:

```bash
NEXT_PUBLIC_AXSDK_API_KEY=your_api_key_here
NEXT_PUBLIC_AXSDK_APP_ID=your_app_id_here
```

- `your_api_key_here`를 Step 2에서 생성한 **API 키**로 교체합니다.
- `your_app_id_here`를 Step 1에서 앱을 만들 때 정의한 **App ID**로 교체합니다.
- `NEXT_PUBLIC_` 접두사는 `AXSDK.init()`이 작동하는 데 필요한 브라우저(클라이언트 사이드) 접근을 위해 이 변수들을 노출합니다.

!!! warning
    절대로 `.env`를 버전 관리에 커밋하지 마세요. 인증 정보를 안전하게 보호하기 위해 `.gitignore` 파일에 추가하세요.

## Step 6: 대화형 내비게이션 테스트

AXSDK 통합이 완료되었습니다. Next.js 개발 서버를 시작하고 브라우저에서 앱을 열어보세요.

```bash
npm run dev
```

앱이 실행되면 화면 **오른쪽 하단 모서리**에 플로팅 **AX 버튼**이 나타납니다. 버튼을 클릭하면 AXSDK 채팅 위젯이 열립니다.

내비게이션을 테스트하기 위해 다음 예시 쿼리를 입력해 보세요:

- `sitemap?` — 에이전트에게 사이트 구조를 설명하도록 요청합니다
- `what services do you have?` — **Services** 섹션으로 이동하거나 설명합니다
- `careers` — **Careers** 페이지로 직접 이동합니다
- `events` — **Events** 페이지로 이동합니다

통합이 완전히 작동하고 있습니다. 에이전트가 요청을 잘못 이해하는 경우, AXSDK 대시보드에서 **System Prompt를 수정**하고 다시 테스트해 보세요.

**중요:** 앱 설정(**System Prompt**, **Sitemap**, **Tool Schema** 등)을 업데이트한 후에는, 새 설정이 적용되려면 반드시 대화를 **재시작**해야 합니다 — 채팅 위젯 왼쪽 하단의 **휴지통 아이콘**을 사용하여 대화를 초기화하세요.

!!! note
    **휴지통 아이콘**이 Next.js DevTools 오버레이에 가려질 수 있습니다 — 보이지 않는다면 **DevTools** 패널을 이동해 보세요.

!!! tip
    **휴지통 아이콘**은 현재 대화 컨텍스트를 초기화하여 에이전트가 최신 설정을 다시 불러오도록 강제합니다. AXSDK 대시보드에서 변경을 가한 후에는 항상 대화를 재시작하세요.

## 마무리

이 튜토리얼에서 다음 내용을 완료했습니다:

- **Sitemap**, **System Prompt**, **Tool Schema**, **Domain Whitelist**, **BYOK** 설정을 포함한 AXSDK 앱 생성 및 구성
- AXSDK 패키지 설치 및 `AXProvider` 컴포넌트 빌드
- `layout.tsx`를 사용하여 Next.js 루트 레이아웃에 위젯 마운트
- 자연어 쿼리를 사용한 대화형 내비게이션 테스트

이 예제의 전체 소스 코드는 GitHub에서 확인할 수 있습니다:

**[https://github.com/layorixinc/axsdk-examples/tree/main/packages/navigation](https://github.com/layorixinc/axsdk-examples/tree/main/packages/navigation)**

전체 프로젝트를 탐색하여 구현 맥락을 확인해 보세요. 여기서 더 나아가 추가 페이지를 커버하도록 사이트맵을 확장하거나, 에이전트 동작을 개선하기 위해 시스템 프롬프트를 커스터마이징하거나, 더 복잡한 애플리케이션에 동일한 대화형 내비게이션 패턴을 적용할 수 있습니다.

---

## 베타 상태에 대한 안내

AXSDK는 현재 **베타** 단계에 있으며, 예상치 못한 동작이나 미완성 부분을 경험할 수 있습니다. 기대한 대로 작동하지 않는 경우, 먼저 **앱 설정**(Sitemap, System Prompt, Tool Schema, Domain Whitelist)과 **BYOK 설정**을 다시 확인해 보세요.

여러분의 피드백은 AXSDK의 방향을 결정하는 데 매우 소중합니다. 문제가 발생하거나 제안 사항이 있으시면 아래로 연락해 주세요:

**[layorixinc@gmail.com](mailto:layorixinc@gmail.com)**
