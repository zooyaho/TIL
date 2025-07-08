# TIL - 📘 클린 코드 React: 상태(State) 관리

## ✅ 상태란?
컴포넌트에서 시간이 지남에 따라 **변경되는 값**을 의미하며, `useState`, `useReducer` 등을 통해 관리한다.

---

## 🟡 상태로 관리해야 할 값 vs 하지 말아야 할 값

### ✅ 상태로 관리해야 할 값
- 사용자 입력 값 (`input`, `checkbox`, etc.)
- 서버에서 받아온 데이터
- 사용자의 행동에 따라 변경되는 값

### ❌ 상태로 관리하지 말아야 할 값 (파생값)
- **다른 값들로부터 계산 가능한 값**
  - 예: `isLogin = hasToken && isValidToken && !isNewUser`
- 이런 값은 상태로 관리하지 않고, **렌더링 시 계산하는 방식**이 유지보수에 더 안전하다.

---

## ❌ 안 좋은 예시

```tsx
const [isLogin, setIsLogin] = useState(false);

useEffect(() => {
  const isLogin =
    hasToken &&
    hasCookie &&
    isValidCookie &&
    isNewUser === false &&
    isValidToken;

  if (isLogin) {
    setIsLogin(true);
  }
}, [hasToken, hasCookie, isValidCookie, isNewUser, isValidToken]);
```

- 단순 조건을 매번 계산해서 상태로 저장
- `isLogin` 값은 다른 값에 의존하므로 이중 관리가 됨
- 상태 동기화 오류 가능성이 있음

---

## ✅ 좋은 예시 (파생값으로 계산)

```tsx
function FlagState() {
  const isLogin =
    hasToken &&
    hasCookie &&
    isValidCookie &&
    isNewUser === false &&
    isValidToken;

  return <div>{isLogin && '안녕하세요! 반갑습니다'}</div>;
}
```

- `isLogin`을 불필요하게 상태로 두지 않음
- 다른 값이 변경되면 자동으로 최신 상태로 반영됨
- 간결하고 안전함

---

### 💡 실무 팁

- 값이 **직접적으로 바뀌는 것**: `useState`
- 값이 **다른 상태에서 계산될 수 있는 것**: 그냥 변수로!

