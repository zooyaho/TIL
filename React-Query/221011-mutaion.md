# Mutaion(변이): 서버 데이터 업데이트하기
- `낙관적 업데이트(Optimistick Update)`: 여기서 **낙관적이란 변이가 성공하기를 희망하지만 실패하더라도 롤백할 수 있다는 의미이다.**
- 변이에 대한 전역 페칭(Global Fetching), 인디케이터 및 오류 처리(Error Handling) 설정
- 로그인 상태에서만 변이가 가능하게(예약 가능하게, 서버에 데이터가 업데이트 가능하게!) 만들어야 한다.

## ● mutaions의 옵션
- QueryClient의 defaultOptions옵션에서 mutations에 설정

### 인디케이터 및 오류 처리(Error Handling) 설정

👾 오류 처리(Error Handling)
```js
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      ...
    },
    mutations: {
      onError: queryErrorHandler, // 오류에 대한 기본값 설정
    },
  },
});
```

👾 인디케이터 처리(로딩 처리)
```js
// Loading.tsx
import { useIsFetching, useIsMutating } from 'react-query';

export function Loading(): ReactElement {
  const isFetching = useIsFetching();
  const isMutating = useIsMutating();

  const display = isFetching || isMutating ? 'inherit' : 'none';
  
  return (
    <Spinner>
      <Text>Loading...</Text>
    </Spinner>
  );
}
```

## ✨ useMutaion
- useMutaion은 일회성이기 때문에 **캐시 데이터가 없다.**
- 서버에 있는 데이터를 변경한다.
- fetching이나 re-fetching, updating할 데이터가 있는 useQuery와 다르다!
- **기본적으로 재시도가 없다.** 관련된 데이터가 없으므로 refetch하지 않음.
- 캐시 데이터가 없으므로 isLoading, isFetching을 구분하지 않으므로 **isFetching만 있다!**
- useMutaion은 반환 객체에서 `mutaion함수`를 반환한다!
- `onMutate콜백`: 낙관적 쿼리에서 사용하고, 변이가 실패할 때 복원할 수 있도록 이전 상태를 저장하는데 사용한다.

### ⭐️ UseMutateFunction<TData= unknown, TError= unknown, TVariables= void, TContext= unknown>
- 커스텀 훅에서 mutate함수를 반환하는 유형
- `TData`: 변수 함수 자체에서 반환된 데이터 유형
- `TError`: 변이 함수에서 발생할 것으로 예상되는 오류 유형
- `TVariables`: mutate함수가 예상하는 변수(Variables) 유형
-  `TContext`: context는 낙관적 업데이트 롤백을 위해 `onMutate`에 설정하는 유형

### 👾 예제

```js
// useReserveAppointment.tsx
export function useReserveAppointment(): UseMutateFunction<
  void,
  unknown,
  Appointment,
  unknown
> {
  const { user } = useUser();
  const toast = useCustomToast();

  const { mutate } = useMutation((appointment: Appointment) =>
    setAppointmentUser(appointment, user?.id),
  );

  return mutate;
}
```

```js
// Appointment.tsx
const reserveAppointment = useReserveAppointment();
 return (
    <Button
      onClick={() => reserveAppointment(appointmentData)}
    >예약 버튼</Button>
);
```

## ● Invalidation(무효화)
- 변이 후에 데이터가 최신이 아니면 데이터를 다시 가져와야한다. 
- 관련 쿼리를 무효화하여 데이터가 최신이 아님을 React Query에 알린다.
- Query Client 메서드로 invalidateQueries를 사용한다.
- 캐시를 무효화하는 이유는 사용자가 페이지를 새로고침 할 필요가 없게 데이터를 업데이트 시킨다.

### ✨ invalidateQueries
- Query Client 메서드
- **쿼리를 stale(만료)로 표시한다.**
- **쿼리가 현재 렌더링 중이면 리페치(refetch)를 트리거한다.**
- 일반적으로 mutate를 호출하면 변이에 있는 onSuccess핸들러가 관련 쿼리를 무효화하고 이에 따라 데이터 리페치가 트리거된다.

#### 👾 예제1 - 예약 추가하기
```js
// useReserveAppointment.tsx
const queryClient = useQueryClient();

  const { mutate } = useMutation(
    (appointment: Appointment) => setAppointmentUser(appointment, user?.id),
    {
      onSuccess: () => {
        queryClient.invalidateQueries([queryKeys.appointments]);
        // 사용자 피드백 제공
        toast({
          title: 'You have reserved the appointment!!',
          status: 'success',
        });
      },
    },
  );
```

#### 👾 예제2 - 예약 삭제하기
```js
// useCancelAppointment.tsx
export function useCancelAppointment(): UseMutateFunction<
  void, // removeAppointmentUser함수는 반환하지 않기 때문에
  unknown, // 오류 유형은 unknown이다
  Appointment, // 인자로 받는 데이터 유형은 Appointment이다
  unknown // onMutate의 context는 unknown이다(onMutate를 실행하지 않고, context가 없기 때문)
> {
  const queryClient = useQueryClient();
  const toast = useCustomToast();

  const { mutate } = useMutation(
    // (appointment: Appointment) => removeAppointmentUser(appointment),
    // mutate함수 호출 시 인자에 appointment를 전달 받는데, 이때 자동으로 removeAppointmentUser인자에 appointment값이 전달 되기 때문에 익명함수로 작성하지 않아도 된다!
    removeAppointmentUser,
    {
      onSuccess: () => {
        queryClient.invalidateQueries([queryKeys.appointments]);
        toast({
          title: 'You have cancled the appointement',
          status: 'warning',
        });
      },
    },
  );

  return mutate;
}
```

#### 쿼리 키 접두사 적용하기
- 모든 예약 관련 쿼리를 무효화 시킨다
- 동일한 쿼리 키 접두사로 서로 관련된 쿼리를 설정하면 모든 쿼리를 한 번에 무효화할 수 있다.
- 정확한 키로 설정하고 싶으면 `exact: true`로 설정하면 된다!

![](https://velog.velcdn.com/images/zooyaho/post/2ae5e16a-fc67-472c-b3b0-3b75ac33918d/image.png)

- 사용자 예약에 대한 사용자 키를 업데이트 할것이며, user-appointments로 된 문자열을 [ queryKeys.appointments , queryKeys.user , user?.id ] 배열로 업데이트 한다.

👾 예제
```js
/* #01. useUserAppointments() */
const { data: userAppointments = fallback } = useQuery(
    // 'user-appointments',
    [queryKeys.appointments, queryKeys.user, user?.id],
    () => getUserAppointments(user),
    {
      enabled: !!user, // user가 참인지 거짓인지 확인, 거짓이면 해당 쿼리를 비활성화 한다!
    },
  );

/* #02. clearUser() */
 queryClient.removeQueries([
      // 쿼리 키에 두 가지가 첫 항목으로 포함 되어있으면 사용자 ID는 지정할 필요가 없다.
      queryKeys.appointments,
      queryKeys.user,
      // user?.id,
    ]);
```

## 🔎 JsonPatch
- JsonPatch 방식은 커맨드 방식으로 동작한다.
- op, path, value 3개의 항목으로 구성되어 있으며 각 항목이 의미하는것은 아래와 같다. (순서는 관계없다.)
- op : 작업유형 (add, remove, replace, move, copy or test 중에 하나만 사용가능)
- path : 변경할 데이터 경로
- value : 변경할 값
- content-type : application/json-patch+json

## ● React Query의 낙관적 업데이트(Optimistick updates)
- 서버로부터 응답을 받기 전에 사용자 캐시를 업데이트 하는 것
- 장점으로는 서버 응답 받기 전 캐시가 먼저 업데이트 되기 때문에 사용자에게 빠른 경험을 줄 수 있다. 
- 단점으로는 서버 응답이 실패 했을 경우 롤백 로직을 작성해야하기 때문에 코드가 복잡해진다. 이 경우 **업데이트 이전의 데이터를 저장 해둬야하는데 onMutate콜백을 사용한다.**
- **onMutate콜백은 context값을 반환하고 onError 핸들러가 이 context값을 인수로 받아 캐시값을 이전 값으로 복원한다.**
- 캐시를 업데이트할 데이터를 포함하는 특정 쿼리에서 onMutate함수는 진행 중인 모든 refetch를 취소한다.
![](https://velog.velcdn.com/images/zooyaho/post/5a9d019e-71d4-4acc-9f08-b2b12e49b330/image.png)
- 사용자가 업데이트를 트리거(mutate 호출 트리거) > 변이 함수 실행 되어 서버에 데이터 업데이트 
- onMutate실행 > 진행 중인 쿼리를 중단 > 이전 캐시값을 onMutate에서 반환된 context로 저장
- 서버 업데이트가 성공 했다면 > 서버에서 최신 데이터를 가져올 수 있도록 쿼리를 무효화
- 서버 업데이트가 실패 했다면 > onError콜백 실행 > onMutate에서 반환된 context를 사용하여 캐시를 낙관적 업데이트 전 상태로 복원 시킨다

### ⭐️ 쿼리 취소 요청
- **취소 프러퍼티를 가진 promise를 반환하는 쿼리 함수가 필요함.**
- 취소 프러퍼티는 쿼리를 취소하는 함수이다!
- 네트워크 호출 방식이 다르면 취소 방식도 다르다
- axios는 취소 토큰을 사용한다.
- React Query에서 쿼리 취소를 요청 하면 이 취소 함수가 실행 된다.

#### ✔️ AbortController
- React Query는 `AbortController 인터페이스`로 쿼리를 취소한다. 표준 Javascript 인터페이스이다.
- `AbortSignal`객체를 DOM요청에 보낸다.
- React Query에서 axios 쿼리를 수동으로 취소하려면 axios에 중단한다는 신호를 전달해야 한다. 이 중단한다는 신호를 쿼리함수에 인수로 전달된다.

🔗 AbortController 사용가능한 버전
![](https://velog.velcdn.com/images/zooyaho/post/9b9d81e0-5931-4378-a56e-b3a98e78f3a2/image.png)

👾 예제 - siganl사용
- getUser는 낙관적 업데이트 대비 상대적으로 오래 되었을 수 있는 데이터를 서버로부터 가져오는 쿼리이다. 그러므로 수동으로 취소할 수 있도록 설정해 줘야 하는 쿼리 함수이다.

```js
// getUser
async function getUser(
  user: User | null,
  signal: AbortSignal, // AbortSignal타입 지정
): Promise<User | null> {
  if (!user) return null;
  const { data }: AxiosResponse<{ user: User }> = await axiosInstance.get(
    `/user/${user.id}`,
    {
      headers: getJWTHeader(user),
      signal, // signal 전달
    },
  );
  return data.user;
}

// useUser
const { data: user } = useQuery(
    queryKeys.user,
  // signal를 구조분해할당 하여 인수로 전달
    ({ signal }) => getUser(user, signal),
    {
      initialData: getStoredUser,
      onSuccess: (received: User | null) => {
        if (!received) {
          clearStoredUser();
        } else {
          setStoredUser(received);
        }
      },
    },
  );
```
![](https://velog.velcdn.com/images/zooyaho/post/c64053b8-3077-44f7-8177-cb9ee37a427a/image.png)

- useQuery가 AbortController를 관리
- AbortController는 쿼리 함수인 getUser에 전달되는 신호를 생성하고 getUser는 해당 신호를 Axois에 전달 
- 따라서 Axois는 해당 신호에 연결된 상태이다.
- 취소 이벤트에 대하여 해당 신호를 수신한다.
- Axios 호출 등 신호를 청취하는 모든 객체는 해당 취소 이벤트를 수신하고 중단하게 된다!

#### ✔️ cancelQuery
- QueryClient메서드
- **cancelQuery를 AbortController를 관리하는 동일한 키에 실행하는 경우 AbortController에 취소 이벤트를 전달한다!**

### 👾 예제
- 서버가 불안전한 상태에서 사용자 데이터 수정 후 submit 시 쿼리 취소 요청

```js
// usePatchUser.ts

/*
- 사용자 데이터 업데이트를 백드롭으로 사용해서 변이를 보낼 때 서버가 보낸 응답에서 캐시를 업데이트 하는 방법
- usePathUser라는 훅은 서버에서 사용자를 업데이트하는데에 사용한다.
- onSuccess는 서버로부터 응답을 받아 해당 데이터를 사용해 쿼리 캐시를 업데이트한다
- useUser에 updateUser함수가 사용자의 state를 업데이트하고 쿼리 캐시, 로컬스토리지도 업데이트한다. updateUser는 인수로 사용자 데이터를 인식한다. 서버에서 받은 사용자 데이터를 인수로 updateUser에 전달한다.
*/

async function patchUserOnServer(
  newData: User | null,
  originalData: User | null,
): Promise<User | null> {
  if (!newData || !originalData) return null;
  // create a patch for the difference between newData and originalData
  // 두개의 JSON 데이터가 다른지 확인
  const patch = jsonpatch.compare(originalData, newData);

  // send patched data to the server
  const { data } = await axiosInstance.patch(
    // 서버에서 인증 보호된 라우터와 헤더를 보낸다.
    `/user/${originalData.id}`,
    { patch },
    {
      headers: getJWTHeader(originalData),
    },
  );
  return data.user;
}

export function usePatchUser(): UseMutateFunction<
  User,
  unknown,
  User,
  unknown
> {
  const { user, updateUser } = useUser();
  const toast = useCustomToast();
  const queryClient = useQueryClient();

  const { mutate: patchUser } = useMutation(
    (newUserData: User) => patchUserOnServer(newUserData, user),
    {
      // onMutate returns context that is passed to onError
      // 변이 함수로 전달된 모든 데이터를 onMutate 콜백으로 전달 받는다
      onMutate: async (newData: User | null) => {
        // 사용자 데이터를 대상으로 한 발신하는 쿼리는 모두 취소한다.
        // 오래된 서버 데이터는 낙관적 업데이트를 덮어쓰지 않는다.
        queryClient.cancelQueries(queryKeys.user);
        // 기존 사용자 값의 스냅샷을 찍고
        const previousUserData: User = queryClient.getQueryData(queryKeys.user);
        // 새로운 값으로 캐시를 낙관적 업데이트하고
        updateUser(newData);
        // 이후 해당 context를 반환한다
        return { previousUserData };
      },
      onError: (error, newData, previousUserDataContext) => {
        // 오류가 있는 경우 저장된 값으로 캐시를 롤백한다
        // error, newData는 사용하지 않음
        // previousUserDataContext = { previousUserData }를 가진 객체
        if (previousUserDataContext.previousUserData) {
          updateUser(previousUserDataContext.previousUserData);
          // 다시 복귀된 이유를 피드백으로 제공
          toast({
            title: 'Update failed: restoring previous values',
            status: 'warning',
          });
        }
      },
      // onSuccess는 변이 함수에서 반환된 모든 값을 인자로 받는다!!
      onSuccess: (userData: User | null) => {
        if (userData) {
          // 이미 완료되어 사용자를 더 이상 업데이트할 필요가 없다 > onMutate에서 업데이트 함!
          // updateUser(userData);
          toast({ title: 'User Updated!', status: 'success' });
        }
      },
      onSettled: () => {
        // 변이를 resolved 했을 때 성공여부와 관계 없이 onSettled콜백을 실행한다
        // 사용자 데이터를 무효화하여 서버에서 최신 데이터를 보여줄 수 있도록 한다
        queryClient.invalidateQueries(queryKeys.user);
        // 쿼리가 무효화되면 리페치를 실행하여 데이터가 서버측과 동일하게 만든다.
      },
    },
  );

  return patchUser;
}
```
```js
// useUser.ts
async function getUser(
  user: User | null,
  signal: AbortSignal,
): Promise<User | null> {
  // userID가 필요하기 때문에 인수로 user데이터를 받아와야 한다.
  if (!user) return null; // 로그인한 사용자가 없으면 서버에 가지 않고 null을 반환한다.
  const { data }: AxiosResponse<{ user: User }> = await axiosInstance.get(
    `/user/${user.id}`, // 로그인한 사용자의 userId 데이터를 서버에서 가져온다.
    {
      // userId에 대한 데이터를 가져올 권한이 있는지 서버에서 확인하려면 JWTHeader를 포함해야 한다.
      headers: getJWTHeader(user), // { Authorization: `Bearer ${user.token}` }
      signal,
    },
  );
  return data.user;
}

interface UseUser {
  user: User | null;
  updateUser: (user: User) => void;
  clearUser: () => void;
}

/* useUser의 역할은 localStorage와 query cache에서 사용자의 상태를 유지하는 것 */
export function useUser(): UseUser {
  const queryClient = useQueryClient();
  // 기존 user의 값을 이용해서 user의 값을 업데이트한다.
  const { data: user } = useQuery(
    queryKeys.user,
    ({ signal }) => getUser(user, signal),
    {
      // const storedUser = localStorage.getItem(USER_LOCALSTORAGE_KEY);
      // return storedUser ? JSON.parse(storedUser) : null;
      initialData: getStoredUser,
      // updateUser와 clearUser에서 값을 가져오면 각각 User와 null을 가져오므로 해당 타입 설정
      onSuccess: (received: User | null) => {
        if (!received) {
          // localStorage.removeItem(USER_LOCALSTORAGE_KEY);
          clearStoredUser();
        } else {
          // localStorage.setItem(USER_LOCALSTORAGE_KEY, JSON.stringify(received));
          setStoredUser(received);
          // setUser(data); > state에 사용자 데이터 저장
        }
      },
    },
  );

  // meant to be called from useAuth
  function updateUser(newUser: User): void {
    // 사용자 로그인이나, 사용자 정보 업데이트를 처리
    // setUser(newUser); > state에 사용자 데이터 업데이트
    setStoredUser(newUser);
    queryClient.setQueryData(queryKeys.user, newUser);
  }

  return { user, updateUser, clearUser };
};
```


