# 구현 가이드

AXSDK로 Agentic UX를 구현하는 것은 간단한 2단계 프로세스입니다.

## 1단계: 관리자 페이지 설정

**참고: 코드 작성 전에 반드시 완료해야 하는 중요한 단계입니다.**

코드베이스에 들어가기 전에, AXSDK 관리자 대시보드에서 에이전트의 동작을 설정해야 합니다. 여기에는 다음이 포함됩니다:

* **시스템 프롬프트:** AI 에이전트의 페르소나, 제한 사항, 일반 지침을 정의합니다.
* **사이트맵:** 에이전트에게 애플리케이션의 구조와 내비게이션 경로를 이해시킵니다.
* **AXHandler 함수 스키마:** 에이전트가 호출할 수 있는 함수의 스키마를 등록합니다. 이를 통해 AI 엔진에게 어떤 기능이 사용 가능한지, 어떤 파라미터가 필요한지를 정확히 알려줍니다.

## 2단계: 코드 구현

관리자 설정이 완료되면, 다음 단계는 애플리케이션 로직을 에이전트와 연결하는 것입니다. `AXHandler`에 기존 API를 선언하여 이를 수행합니다.

다음은 표준 구현 예시입니다:

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

내부 함수를 `AXHandler`에 매핑함으로써, AI 엔진은 사용자 의도에 따라 복잡한 UI 내비게이션 없이 해당 함수들을 원활하게 호출할 수 있습니다.

### React용 AXProvider 만들기

React 애플리케이션에서 `AXHandler`는 내부 상태, 다른 프로바이더, 또는 스토어에 접근해야 할 수 있습니다. 이를 위해 개발자는 `AXHandler`용 `AXProvider`를 작성해야 할 수 있습니다.

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

## 3단계: UI 마운트 (@axsdk/react)

핸들러와 프로바이더가 준비되면, React 프로바이더와 컴포넌트를 사용하여 애플리케이션 상태에 바인딩합니다.

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
      {/* 플로팅 Agentic UX 인터페이스를 드롭인으로 추가 */}
      <AXUI />
    </main>
  );
}
```

### 전체 동작 흐름
1. 사용자가 `AXUI` 컴포넌트를 통해 자연어로 의도를 입력합니다.
2. `@axsdk/core`가 백엔드 AI 에이전트와 함께 이 입력을 처리합니다.
3. `@axsdk/core`는 등록된 `AXHandler 스키마`에 따라 `AXHandler`를 호출합니다(예: `"AX_add_to_cart"` 같은 명령어 실행).
4. 네이티브 코드가 Proxy를 통해 안전하게 실행됩니다. `AXProvider`와 통합되어 있어 React 상태, 훅, 스토어에 완전히 접근할 수 있으며, UI를 즉시 업데이트할 수 있습니다.
