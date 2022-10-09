# 사용자 로그인
- `setQueryData`: 실제로 캐시에 데이터를 설정하기 위함
- `removeQuries`: 캐시에서 쿼리를 삭제하기 위함

## ⭐️ JWT(JASON Web Token)
- 인증 토큰
- **JWT는 클라이언트가 사용자 명과 비밀번호를 보내고 이것이 데이터베이스에서 일치한다면 서버가 토큰을 보내는 방식으로 작동한다!**
- 언제든 클라이언트가 필요로 하면 로그인이 필요한 서버에서 클라이언트는 헤더의 토큰을 요청과 함께 보낸다. 그러면 서버가 이 클라이언트가 인증 됐음을 인지한다.

### 🔐 보안
- `서버`가 토큰을 생성하고 사용자 명, 사용자 ID와 같은 정보를 인코딩한다.
- 인코딩 된 토큰이 서버로 돌려 보내지면 디코딩이 되고 데이터가 일치함을 확인할 수 있다. 그래서 서버를 설치할 때 암호가 필요한 것이다.
- 토큰을 인코딩, 디코딩하는 데 그 암호가 필요한 것이다.

✔️ 예제에서의 보안
- 이 예제에서는 토큰이 사용가 객체에 저장되어 있다.
- 서버로부터 전달된 사용자 객체가 이 토큰을 담고 있고 클라이언트에서 사용했듯이 이 사용자 객체도 토큰을 담고 있다.
- 이 예제는 localStorage에 토큰을 유지한다. 그래서 페이지 새로고침을 하더라도 사용자가 로그아웃이 되지 않는 것이다.

## 🧐 누가 사용자 데이터를 소유해야할까?
- React Query의 책임은 클라이언트의 서버 상태를 관리하는 것이다.
- useAuth 훅의 책임은 signIn, signUp, signOut 함수를 제공하는 것이다. 그래서 서버에 있는 사용자를 인증할 수 있는것이다.
- 결론은 데이터 저장은 React Query에 하는게 맞다! 그러려면 useUser라는 훅이 필요한 것이다!!!
- React Query는 데이터를 저장하고 useAuth는 지원을 한다. 서버를 호출할 때 useAuth는 사용자 데이터를 수집한다.
- 서버는 useAuth의 signIn, signUp 호출 시 데이터를 반환한다!
- useUser의 함수로 이런 내용을 캐시에 추가하면 된다.

### 🔎 useUser 훅의 역할
- 쿼리 캐시에 사용자 데이터를 저장하기 때문에 useUser은 사용자 데이터를 반환해야한다.(객체 반환)
- localStorage의 데이터를 로딩해서 초기 설정을 한다. -> 사용자가 페이지를 새로고침해도 데이터를 유지하는 방법이다.
- useQuery 인스턴스의 쿼리 함수는 로그인 한 사용자의 ID와 함께 서버에 요청을 보낸다. 그럼 서버가 그 사용자에 관한 데이터를 돌려보내 준다. 만약 로그인한 사용자가 없다면 쿼리 함수는 null를 반환할 것이다.
- useUser의 역할은 우리 앱의 특정 인스턴스까지 로그인한 사용자를 추적하는 것이다.
- 정보가 업데이트 되면 setQueryData로 직접 React Query 캐시를 업데이트 한다. 그리고 localStorage도 업데이트 한다.
- localStorage 업데이트는 onSuccess 콜백에서 진행되며 useQuery까지 업데이트 한다.
- onSuccess 콜백은 setQueryData와 쿼리 함수가 실행된 이후에 실행된다.
- 어떤 방식으로든 쿼리 캐시는 업데이트 되는데 setQueryData를 통해 업데이트가 되거나 쿼리 함수가 실행될 때 생긴 변이 뒤에 업데이트 될 수도 있다.
- 어떤 방법이든 onSuccess 콜백은 localStorage를 업데이트 한다.

### 🧐 사용자 데이터를 Auth Provider에 저장할 수는 없을까?
- 옵션으로 가능하다.
- 단점으로는 이미 복잡한 시스템에 복잡함을 더한다는 것이다.
- 불필요한 데이터가 생긴다는 것도 단점이다. 사용자 변이를 허용한다면 그 사용자 데이터를 React Query에 보관 하고 싶을 때, Auth Provider에도 사용자 데이터를 입력해야 한다.

### 🔎 useAuth훅의 역할
- useAuth의 역할은 signin, signup, signout 함수들이 서버와 통신하도록 하는 것이다
- 쿼리 캐시에 값을 설정한다
- React Query는 인증 제공자 역할을 하며 React Query cache는 user값을 필요로하는 모든 컴포넌트에 user값을 제공한다.
- 쿼리 캐시에 값을 설정하기 위해 `queryClient.setQueryData`를 사용한다. 쿼리 키와 값을 가져와 쿼리캐시에 해당 키에 대한 값을 설정할 수 있다.
- useUser훅의 updateUser, clearUser에`queryClient.setQueryData`를 작성하고, useAuth에서는 이 함수들을 호출하여 사용한다!

## ● setQueryData
- QueryClient의 메서드이다.

```js
// useUser.tsx
const queryClient = useQueryClient();

// meant to be called from useAuth.tsx
function updateUser(newUser: User): void {
  // 사용자 로그인이나, 사용자 정보 업데이트를 처리
  queryClient.setQueryData(queryKeys.user, newUser); // onSuccess를 트리거한다.
}

// meant to be called from useAuth.tsx
function clearUser() {
  // 로그아웃 처리
  queryClient.setQueryData(queryKeys.user, null); // onSuccess를 트리거한다.
}
```

## 로그인 유지하기
- 새로고침해도 로그인 상태를 유지하기 위해 localStorage에 사용자 데이터를 저장한다.
- **useUser의 useQuery가 초기 실행될 때 해당 localStorage를 초기 데이터로 사용한다!!**
- 초기 실행 시 localStorage에서 로드하여 페이지를 새로 고침해도 React Query가 사용자를 잃지 않도록 하는 것이다.
- 애초에 localStorage가 사용자 데이터로 채워져 있어야 localStorage에서 로드하는 것이 가능하므로 useQuery의 onSuccess 콜백으로 localStorage를 업데이트 해야한다.
- onSuccess 콜백은 useAuth 함수 작업 시 사용하는 queryClient.setQueryData 실행 이후나 쿼리함수가 반환된 후에 실행된다.

### ● onSuccess 옵션
- useQuery의 옵션
- onSuccess는 쿼리 함수나 setQueryData에서 데이터를 가져오는 함수이다.(둘 다에서 실행되기 때문이다)

```js
// 기존 user의 값을 이용해서 user의 값을 업데이트한다.
const { data: user } = useQuery(queryKeys.user, () => getUser(user), {
    // updateUser와 clearUser에서 값을 가져오면 각각 User와 null을 가져오므로 해당 타입 설정
    onSuccess: (received: User | null) => {
      if (!received) {
        // localStorage.removeItem(USER_LOCALSTORAGE_KEY);
        clearStoredUser();
      } else {
        // localStorage.setItem(USER_LOCALSTORAGE_KEY, JSON.stringify(user));
        setStoredUser(received);
      }
    },
});
```

🚨 [문제] 위와 같이 작성할 경우 새로고침해도 로그인 상태가 유지 되지 않는다
💡 [해결] 사용자 데이터가 localStorage에 정의되어있는지 확인해야한다 -> initailData옵션을 사용한다.

### ● initailData 옵션
- useQuery의 옵션
- 초기 데이터를 캐시에 추가하고 싶을 때 사용한다.
- fallback, placeholderData옵션을 사용할 수 있지만 이 둘은 캐시에 추가되지 않는다!!!
- 하지만 사용자의 경우 캐시에 추가해야한다!

```js
 // 기존 user의 값을 이용해서 user의 값을 업데이트한다.
const { data: user } = useQuery(queryKeys.user, () => getUser(user), {
    // const storedUser = localStorage.getItem(USER_LOCALSTORAGE_KEY);
    // return storedUser ? JSON.parse(storedUser) : null;
    initialData: getStoredUser,
    onSuccess: (received: User | null) => {
      if (!received) {
        clearStoredUser();
      } else {
        setStoredUser(received);
      }
    },
});
```

## ● removeQuries
- 로그아웃 시 사용자 예약 쿼리 삭제하기
- queryClient 메서드
- 특정 쿼리에 대한 데이터를 제거한다.

### 🧐 user데이터에 removeQuries를 사용하지 않고 setQueryData에 null을 사용하는 이유?
- 사용자 데이터를 변경해서 onSuccess 콜백을 발생시킬 때 onSuccess콜백이 로컬 스토리지에 데이터를 유지하며 setQueryData가 onSuccess를 발생시키기 때문이다.
- **removeQuries가 실행하고 onSuccess가 실행되지 않는다!!!**
- userAppointments는 로컬 스토리지에 존재하지 않으므로 useUser에 onSuccess를 필요하지 않다

```js
// useUser.tsx
function clearUser() {
    // 로그아웃 처리
    queryClient.setQueryData(queryKeys.user, null); // onSuccess를 트리거한다.
    queryClient.removeQueries('user-appointments');
}
```
👉🏻 하나 이상의 쿼리키에 removeQueries를 실행하려면 removeQueries를 여러번 동일하게 실행하면 된다.