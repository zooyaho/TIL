> Next.js는 서버와 클라이언트 간의 데이터 패칭과 상태 관리를 간편하게 처리할 수 있는 강력한 프레임워크입니다. 특히, `@tanstack/react-query`를 사용하면 데이터 패칭을 효율적으로 관리할 수 있습니다. 이번 포스트에서는 Next.js의 `앱 라우터`에서 `쿼리 클라이언트`를 어떻게 생성하고 관리해야 하는지에 대해 알아보겠습니다.

## 1. React Query란?

React Query는 React 애플리케이션에서 서버 상태를 쉽게 관리할 수 있도록 도와주는 라이브러리입니다. API 요청을 수행하고, 응답을 캐시하고, 자동으로 업데이트하는 등의 기능을 제공합니다. 이를 통해 복잡한 데이터 패칭 로직을 간소화하고, 사용자 경험을 개선할 수 있습니다.

## 2. Next.js의 앱 라우터

Next.js의 앱 라우터는 페이지 기반의 라우팅 시스템을 제공합니다. 각 페이지는 독립적인 경로를 가지며, 필요에 따라 **서버 사이드 렌더링(SSR)**이나 **클라이언트 사이드 탐색**을 통해 데이터를 패칭할 수 있습니다. 이를 통해 사용자에게 빠르고 매끄러운 경험을 제공할 수 있습니다.

## 3. 앱 라우터와 페이지 라우터의 데이터 패칭 차이에 따른 React Query 설정

Next.js의 앱 라우터와 페이지 라우터는 데이터 패칭 및 상태 관리 방식에서 차이를 보이며, 이로 인해 React Query를 설정하는 방법도 달라집니다.

### 3.1 Page Router

- **쿼리 클라이언트 관리**: 쿼리 클라이언트는 일반적으로 한 번 생성되어 사용되며, 페이지 전환 시 재사용됩니다. 페이지가 언마운트되면 해당 페이지의 상태는 사라지지만, 캐시된 데이터는 유지될 수 있습니다.

- **서버 데이터 패칭**: 페이지 라우터에서는 `getServerSideProps`, `getStaticProps`, `getInitialProps` 등의 메서드를 사용하여 서버에서 데이터를 패칭합니다. 이 방식에서는 **쿼리 클라이언트를 별도로 관리할 필요가 없으며**, 페이지가 렌더링될 때 필요한 데이터가 props로 전달됩니다.

### 3.2 App Router

- **쿼리 클라이언트 관리**: 쿼리 클라이언트를 페이지 전환 간에 재사용합니다. 이전 페이지의 데이터를 클라이언트에서 하이드레이션할 수 있으며, 이를 통해 상태를 효율적으로 관리할 수 있습니다.

- **서버 데이터 패칭**: 앱 라우터에서는 클라이언트와 서버 간의 상호작용을 더욱 정교하게 처리해야 하므로, **쿼리 클라이언트를 서버에서 생성하고 클라이언트에서 재사용할 수 있도록 설정**해야 합니다. 이 과정에서는 `getQueryClient`와 `ReactQueryStreamedHydration`을 활용하여 서버에서 렌더링된 데이터를 클라이언트에서 `하이드레이션`하는 작업이 필요합니다.

## 4. App Router방식에서 쿼리 클라이언트의 생성 및 관리

Next.js App Router에서 React Query를 사용할 때, **쿼리 클라이언트는 서버와 클라이언트 각각에서 `독립적으로 생성되고 관리`**됩니다.

### 4.1 서버 쿼리 클라이언트

서버에서 쿼리 클라이언트는 요청이 들어올 때마다 **새롭게 생성**됩니다. 이를 통해 최신 데이터를 패칭하고, 클라이언트에 렌더링된 HTML을 전달할 수 있습니다. 서버 쿼리 클라이언트는 `요청의 생애 주기에만 존재하며, 다른 요청과 공유되지 않습니다.`

### 4.2 클라이언트 쿼리 클라이언트

반면에 클라이언트에서 쿼리 클라이언트는 `한 번 생성된 후 재사용`됩니다. 이 방식은 상태 관리 및 데이터 캐싱을 효율적으로 수행하는 데 유리합니다. 페이지를 이동할 때마다 기존 클라이언트를 재사용하여 이전에 패칭한 데이터는 캐시에 남아 있습니다. 동일한 쿼리를 다시 호출할 때, 캐시된 데이터를 사용할 수 있어 불필요한 요청을 줄이고, 성능을 개선합니다.

## 5. React Query 설정 예제

아래는 Next.js의 앱 라우터에서 React Query를 설정하는 코드 예제입니다.

### 5.1 모듈 가져오기

```javascript
// src/providers/query.tsx
"use client";
import {
  QueryClient,
  QueryClientProvider,
  isServer,
} from "@tanstack/react-query";
import { ReactQueryStreamedHydration } from "@tanstack/react-query-next-experimental";
```

- `'use client'`: 클라이언트 컴포넌트에서 실행되도록 지정합니다.
- `QueryClient`: React Query의 **인스턴스**를 생성하는 클래스입니다.
- `QueryClientProvider`: React Query의 **컨텍스트를 제공**하는 컴포넌트로, 하위 컴포넌트에서 쿼리 클라이언트를 사용할 수 있게 해줍니다.
- `isServer`: **현재 코드가 서버에서 실행되고 있는지 여부를 확인**하는 유틸리티입니다.
- ⭐️ `ReactQueryStreamedHydration`: 서버에서 렌더링된 쿼리 데이터를 클라이언트에서 하이드레이션하는 데 사용하는 컴포넌트입니다.

### 5.2 쿼리 클라이언트 생성

```javascript
function makeQueryClient() {
  return new QueryClient({
    defaultOptions: {
      queries: {
        staleTime: 60 * 1000, // 기본 캐싱 시간(1분)
      },
    },
  });
}
```

- `makeQueryClient`: 새로운 QueryClient 인스턴스를 생성하는 함수입니다. 이 인스턴스는 기본 옵션을 설정할 수 있습니다. 여기서는 쿼리가 1분 동안 신선하다고 간주되도록 staleTime을 설정합니다. 이 시간이 지나면 쿼리는 "오래된" 상태로 간주되고, **다음 요청 시 자동으로 다시 요청**됩니다.

### 5.3 쿼리 클라이언트 관리

```javascript
let browserQueryClient: QueryClient | undefined = undefined;

function getQueryClient() {
  if (isServer) {
    return makeQueryClient(); // 서버에서 실행 중인 경우 새 클라이언트를 반환
  } else {
    if (!browserQueryClient) browserQueryClient = makeQueryClient(); // 브라우저에서 클라이언트가 없는 경우 새 클라이언트를 생성
    return browserQueryClient; // 기존 브라우저 클라이언트를 반환
  }
}
```

- browserQueryClient: 브라우저에서 사용할 쿼리 클라이언트를 저장하는 변수입니다.
- getQueryClient: 현재 환경에 따라 적절한 쿼리 클라이언트를 반환하는 함수입니다. 서버에서 실행 중일 때는 새 클라이언트를 생성하고, 브라우저에서는 기존 클라이언트를 재사용합니다.

### 5.4 쿼리 프로바이더 컴포넌트

```javascript
export function QueryProvider({ children }: { children: React.ReactNode }) {
  const queryClient = getQueryClient(); // 쿼리 클라이언트를 가져옴
  return (
    <QueryClientProvider client={queryClient}>
      <ReactQueryStreamedHydration>{children}</ReactQueryStreamedHydration>
    </QueryClientProvider>
  );
}
```

- `QueryProvider`: React 컴포넌트로, 하위 컴포넌트에 쿼리 클라이언트를 제공하는 역할을 합니다.
- `QueryClientProvider`: 쿼리 클라이언트를 children 컴포넌트에 전달합니다.
- `ReactQueryStreamedHydration`: 서버에서 렌더링된 쿼리 데이터를 클라이언트에서 하이드레이션할 수 있도록 합니다.
