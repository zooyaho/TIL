## useIsFetching
- 중앙화된 로딩 인디케이션 적용
- 현재 가져오는 중인 쿼리가 있는지 알려주는 훅
- 각각의 커스텀 훅에 대해 isFetching을 사용할 필요가 없어진다.
- **현재 가져오기 상태인 쿼리 호출의 수를 나타내는 정수값을 반환한다.**
- **useIsFetching이 0보다 크면 가져오기 상태인 호출이 존재하며 참으로 평가된다.**

👾 예제
```js
import { useIsFetching } from 'react-query';

export function Loading(): ReactElement {
  const isFetching = useIsFetching();

  const display = isFetching ? 'inherit' : 'none';

  return (
    <Spinner
      thickness="4px"
      speed="0.65s"
      emptyColor="olive.200"
      color="olive.800"
      role="status"
      position="fixed"
      zIndex="9999"
      top="50%"
      left="50%"
      transform="translate(-50%, -50%)"
      display={display}
    >
      <Text display="none">Loading...</Text>
    </Spinner>
  );
}
```
## onError
- 쿼리함수가 에러를 발생시키면 onError이 실행됨.
- React Query가 콜백에 에러 매개변수를 전달하기 때문에 다양한 방식으로 에러 처리가 가능하다.

### ✔️ useQuery 옵션에 onError 콜백함수를 사용
- 인자로 발생한 error를 받는다.

👾 toast를 사용한 에러처리 예제
```js
// treatments.tsx
import { useQuery } from 'react-query';
import { useCustomToast } from '../../app/hooks/useCustomToast';

async function getTreatments(): Promise<Treatment[]> {
  const { data } = await axiosInstance.get('/treatments');
  return data;
}

// treatments 데이터 반환하는 훅!
export function useTreatments(): Treatment[] {
  const toast = useCustomToast(); // toast custom hook
  const fallback = [];

  const { data = fallback } = useQuery(queryKeys.treatments, getTreatments, {
    onError: (error) => {
      const title =
        error instanceof Error
          ? error.message
          : 'error connecting to the server';
      toast({ title, status: 'error' });
    },
  });

  return data;
}
```

### ✔️ QueryClient 옵션에 onError 콜백함수를 사용
- QueryClient를 위한 onError핸들러 기본값을 만든다.
- QueryClient에는 queries와 mutaions에 대해 기본값을 만들 수 있으면 옵션에 queries, mutaions프로퍼티를 가진다.
```js
{
  // 각각 useQuery, useMutaion에 기본값을 지정할 수 있다.
  queries: { useQuery options },
  mutaions: { useMutaion options },
}
```
- useQuery마다 오류 핸들러를 추가할 필요가 없어진다.

👾 toast를 사용한 에러처리 예제

```js
// queryClient.tsx
import { createStandaloneToast } from '@chakra-ui/react';
import { QueryClient } from 'react-query';

import { theme } from '../theme';

const toast = createStandaloneToast({ theme });

function queryErrorHandler(error: unknown): void {
  const title =
    error instanceof Error ? error.message : 'error connecting to server';

  // toast가 점차 쌓이기 때문에 toast가 중복되지 않도록 함
  toast.closeAll();
  toast({ title, status: 'error', variant: 'subtle', isClosable: true });
}

export const queryClient = new QueryClient({
  // 중앙 집중식 오류 핸들러 지정
  defaultOptions: {
    queries: {
      onError: queryErrorHandler,
    },
  },
});

```