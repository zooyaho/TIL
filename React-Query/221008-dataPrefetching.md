# 🔗 데이터 프리페칭(pre-fetching)

![](https://velog.velcdn.com/images/zooyaho/post/1b582479-a3e3-436b-82ff-a7b9414d30e6/image.png)

- 캐시에 아직 데이터가 없는 경우 미리 데이터를 채울수 있다.
- 유효한 데이터인 경우 채울 수 있디만 자리표시자 데이터인 경우 추가하지 않는다.

## ● prefetchQuery
- queryClient의 메서드이므로 useQuery와 달리 클라이언트 캐시에 추가된다.
- 일회성이다.
- 정적인 데이터를 호출할 때 적합하며, 정적인 컴폰넌트에서 prefetchQuery를 정의하는 것이 좋다.
- 페이지가 처음 불러올 때 prefetchQuery로 클라이언트 캐시에 데이터를 추가하여 useQuery로 데이터를 불러올 동안 추가한 데이터를 보여주는 장점이 있다.(리페칭 동안에는 캐시 된 데이터를 사용자에게 보여준다)
- 사용자 custom 훅내에서 prefetchQuery를 사용할 수 있다.
- 훅이 아닌 경우 훅 내에서 useQueryClient를 실행할 수 없다.

### 👾 예제
- useTreatments.tsx에서 usePrefetchTreatments훅 작성

```js
import { useQueryClient } from 'react-query';

export function usePrefetchTreatments(): void {
  const queryClient = useQueryClient(); // queryClient를 반환
  queryClient.prefetchQuery(queryKeys.treatments, getTreatments);
}
```
![](https://velog.velcdn.com/images/zooyaho/post/95da49f9-c080-4c4c-a1cf-74f9144c1d2f/image.png)
👉🏻 처음 home렌더링 시 treatments 데이터가 캐시에 등록된다.

## ● setQueryData
- queryClient의 메서드이다.
- useQuery를 실행하지 않고 쿼리데이터를 캐시에 추가하는 또 다른 방법

## ● placeholderData
- useQuery의 옵션이다.
- client에서 데이터를 가져오고 캐시에 추가되지 않는다.
- placeholderData는 고정값 또는 함수를 사용할 수 있다.
- 자리 표시자가 필요한 경우에만 사용한다.
- 자리 표시자 데이터값을 동적으로 결정하는 함수를 사용하려는 경우 placeholderData를 사용하는 것이 좋다.
- 다시 사용할 일이 없기 때문에 캐시에 추가하지 않는다.

## ● initialData
- placeholderData 반대의 개념이다.
- useQuery의 옵션이며 client에서 제공한다.
- 캐시에 추가된다.

## ✨ useQuery의 select option
- **query함수가 반환하는 데이터를 변환할 수 있다.**
- React Query는 **불필요한 연산을 줄이기 위해 최적화를 하는데 이를 메모이제이션(Memoization)이라고 한다.**
- select함수를 삼중 등호로 비교하며, 데이터와 함수가 모두 변경되었을 경우에만 실행된다.
- **마지막으로 검색한 데이터와 동일한 데이터이고 셀렉트 함수에도 변동이 없으면 셀렉트 함수를 재실행하지 않는다.**
- 셀렉트 함수에는 안정적인 함수가 필요한데, 매번 바뀌는 익명함수, 즉 삼중등호로 비교하는 함수는 실패한다. **익명함수를 안정적인 함수로 만들고 싶을때는 useCallback 훅을 사용하면 된다.**

### 👾 예제1
- 체크박스 선택 시 캘린더에 예약한 가능한 일정만 보여주는 기능
```js
export function useAppointments(): UseAppointments {
  const currentMonthYear = getMonthYearDetails(dayjs());
  const [monthYear, setMonthYear] = useState(currentMonthYear);

  const [showAll, setShowAll] = useState(false);
  // showAll이 true일 경우 selectedFn를 호출하지 않고 모든 데이터를 반환한다!

  // 로그인한 사용자가 예약한 날짜를 표시하기 위함
  const { user } = useUser();

  // 이 함수는 useAppointments 훅이 실행될 때마다 변경 되기 때문에 useCallback을 실행해야 한다.
  const selectedFn = useCallback(
    (data) => {
      // 변환한 다음 변환한 데이터를 반환한다!

      // 가능한 예약을 모두 반환하는 암시적 반환을 사용한다.
      return getAvailableAppointments(data, user);
    },
    [user], // 사용자가 로그아웃할 때마다 이 함수를 변경해야 한다.
  );

  // 👉🏻 getAppointments(year, month) : year, month 인수로 전달
  // 👉🏻 AppoinmentDateMap을 반환하여 appointments에 할당

  const fallback = {};
  const { data: appointments = fallback } = useQuery(
    [queryKeys.appointments, monthYear.year, monthYear.month],
    () => {
      getAppointments(monthYear.year, monthYear.month);
    },
    { // 🚨 selectedFn의 인자에 자동으로 data가 들어간다!!
      select: showAll ? undefined : selectedFn,
    },
  );

  return { appointments, monthYear, updateMonthYear, showAll, setShowAll };
}
```

### 👾 예제2
- 라디오 버튼 선택 시 해당 staff 출력
```js
export function useStaff(): UseStaff {
  const [filter, setFilter] = useState('all');

  const selectedFn = useCallback(
    (unfilteredStaff) => {
      return filterByTreatment(unfilteredStaff, filter);
    },
    [filter],
  );

  const fallback = [];
  const { data: staff = fallback } = useQuery(queryKeys.staff, getStaff, {
    // 🚨 selectedFn의 인자에 자동으로 data가 들어간다!!
    select: filter === 'all' ? undefined : selectedFn,
  });
  
  return { staff, filter, setFilter };
}
```

# 🔗 리페칭(re-fetching)
- 서버가 만료 데이터를 업데이트한다. 즉, 일정 시간이 지나면 서버가 만료된 데이터를 삭제하는데 이러한 리페칭은 페이지를 벗어났다가 다시 돌아왔을 때 볼 수 있다.

## ⭐️ 리페칭이 일어나는 상황
- stale쿼리는 어떤 조건 하에서 자동적으로 다시 가져오기가 된다. 
- 새로운 쿼리 인스턴스가 많아지는 경우
- 쿼리 키가 처음 호출되는 경우
- 쿼리를 호출하는 반응 컴포넌트를 증가시키는 경우
- 창을 재포커스 하는 경우
- 만료된 데이터의 업데이트 여부를 확인할 수 있는 네트워크가 다시 연결 된 경우
- 리페칭 간격이 지난 경우(이 경우 간격에 리페칭을 해서 서버를 풀링하고 사용자 조치가 없더라도 데이터가 업데이트 된다)

## 🔔 리페칭 방법

1️⃣ 옵션으로 제어하는 방법(전역일수도 있고 호출 쿼리 사용에 특정된 것)
2️⃣ 명령하여 useQuery를 쓰면 객체를 반환 하는데 그 중 refetch메서드가 있다.

### ● refetchOnMount
- 불리언(기본값 true)
### ● refetchOnWindowFocus
- 불리언(기본값 true)
### ● refetchOnReconnect
- 불리언(기본값 true)
### ● refetchInterval
- 밀리초 단위의 시간

## ✔️ 리페칭 제어하는 방법
- refetchOnMount, refetchOnWindowFocus, refetchOnReconnect를 false로 변경한다.
- stale 시간을 증가시킨다.
- **변동이 잦지 않은 데이터에 적용해야 하며 ** 작은 변동에도 큰 변화를 불러오는 데이터에는 적용하지 말아야 한다.

### 👾 예제1 - 개별적으로 리페칭 제어
```js
const { data = fallback } = useQuery(queryKeys.treatments, getTreatments, {
    staleTime: 600000, // 10 minutes
    cacheTime: 900000, // 15 minutes
    refetchOnMount: false,
    refetchOnWindowFocus: false,
    refetchOnReconnect: false,
});
```

### 👾 예제2 - 전역적으로 리페칭 제어
```js
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      onError: queryErrorHandler,
      staleTime: 600000, // 10 minutes
      cacheTime: 900000, // 15 minutes
      refetchOnReconnect: false,
      refetchOnWindowFocus: false,
      refetchOnMount: false,
    },
  },
});
```

### 👾 예제3 - 오버라이딩하여 리페칭 제어
- 전역적으로 리페칭을 제어할 경우 예약 사항이나 사용자 정보와 같은 리페칭이 자주 일어나야하는 것들은 리페칭 설정을 오버라이딩한다!!

```js
// common optios for both useQuery and prefetchQuery
const commonOptions = {
  staleTime: 0,
  cacheTime: 300000, // 5 minutes
};

// prefetchQuery
useEffect(() => {
    const nextMonthYear = getNewMonthYear(monthYear, 1);
    queryClient.prefetchQuery(
      [queryKeys.appointments, nextMonthYear.year, nextMonthYear.month],
      () => {
        getAppointments(nextMonthYear.year, nextMonthYear.month);
      },
      commonOptions, // staleTime과 cacheTime은 pre-fetching에 적용이 되어야하기 때문에 공통으로 분리해서 적용한다!
    );
}, [monthYear, queryClient]);

// useQuery
const { data: appointments = fallback } = useQuery(
    [queryKeys.appointments, monthYear.year, monthYear.month],
    () => {
      getAppointments(monthYear.year, monthYear.month);
    },
    {
      // keepPreviousData: true, // 쿼리 키가 변경될 때까지 이전의 모든 데이터가 그대로 유지된다.
      select: showAll ? undefined : selectedFn,
      ...commonOptions,
      refetchOnMount: true,
      refetchOnWindowFocus: true,
      refetchOnReconnect: true,
    },
);
```

### 👾 예제4 - 폴링: 간격에 따른 자동 리페칭(refetching)
- 주기적으로 자동 리페칭하는 방법
- refetchInterval: 60000 // 1 minutes 을 추가하면 된다!
