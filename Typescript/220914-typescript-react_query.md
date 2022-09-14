# React Query

- 한번 api로 받은 data는 캐쉬에 저장이 되어 loading이 되지 않게 한다.

## 설치

`npm i @tanstack/react-query`

## 1️⃣ provider 설정

- QueryClientProvider안에 있는 컴포넌트는 queryClient를 사용할 수 있다.

👾 index.tsx

```js
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

// Create a client
const queryClient = new QueryClient();

root.render(
  // Provide the client to your App
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
);
```

## 2️⃣ fetcher함수 만들기

- api호출하는 함수
- fetch하는 함수들의 기능을 대신한다.
- fetcher함수는 꼭 fetch promise(json data의 promise)를 return해줘야 한다.
- api.tsx파일 생성해 필요한 fetch함수 생성
- 기본적으로 API와 관련된 것들은 component들과 멀리 떨어지도록 하는것이 좋다.

👾 api.tsx

```js
export async function fetchCoins() {
  return await (await fetch(`https://api.coinpaprika.com/v1/coins`)).json();
}
```

## 3️⃣ useQuery훅 사용

`const {isLoading, data} = useQuery(["queryKey"],fetcher함수, arguments)`

- `isLoading`: isLoading을 반환(boolean), 로딩이 완료되면 false를 반환.
- `data`: fetcher함수에서 반환한 json데이터를 가리킨다.
- `queryKey`: query의 고유식별자
- `fetcher함수`: 2에서 만든 함수를 import해서 등록한다. 데이터를 받아오고 JSON을 리턴하는 함수
- `arguments`: {refetchInterval:500}과 같이 fetch를 5초마다 실행되게 함.

👾

```js
import { useQuery } from "@tanstack/react-query"

interface ICoin {
  id: string;
  name: string;
  symbol: string;
  rank: number;
  is_new: boolean;
  is_active: boolean;
  type: string;
}
...
const { isLoading, data: coins } = useQuery<ICoin[]>(["allCoins"], fetchCoins);
```

👾 fetcher함수에 인수 전달

- 콜백 함수를 사용하여 인수를 전달한다.

```js
const { isLoading: isLoadingInfo, data: info } =
  useQuery < InfoData > (["info", coinId], () => fetchCoinInfo(coinId));
```

## ⭐️ React QueryDevtools

- cash에 있는 query를 시각화해서 볼 수 있다.

`npm i @tanstack/react-query-devtools`

```js
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      {/* The rest of your application */}
      <ReactQueryDevtools initialIsOpen={true} />
    </QueryClientProvider>
  );
}
```

> [React Query 공식문서](https://tanstack.com/query/v4)
