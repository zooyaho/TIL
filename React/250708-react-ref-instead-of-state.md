# TIL - 📘 클린 코드 React: useRef vs useState

## 리렌더링 방지가 필요하다면 useState 대신 useRef
- 컴포넌트의 전체적인 수명과 동일하게 지속된 정보를 일관적으로 제공해야하는 경우
- ✅ useRef는 꼭 DOM 조작만을 위한 용도가 아니다!
---

## 📌 상황: 컴포넌트가 마운트되었는지를 추적할 때

### ❌ 잘못된 방법: useState 사용
```tsx
const [isMount, setIsMount] = useState(false);

useEffect(() => {
  if (!isMount) {
    setIsMount(true);
  }
}, [isMount]);
```
- `setIsMount(true)`로 인해 리렌더링이 발생
- 하지만 `isMount`는 렌더링에 필요 없는 값
- 불필요한 렌더링 낭비 발생

### ✅ 올바른 방법: useRef 사용
```tsx
const isMount = useRef(false);

useEffect(() => {
  isMount.current = true;
}, []);
```
- `useRef`는 값이 변경되어도 리렌더링되지 않음
- 컴포넌트가 마운트되었는지 추적하기 위한 용도로 최적
- **렌더링과 무관한 정보 저장은 useRef를 쓰는 것이 권장**

---

## 🔍 핵심 요약

| 기준                     | `useState`         | `useRef`                                 |
|--------------------------|--------------------|-------------------------------------------|
| 값 변경 시 리렌더링      | ✅ 발생함           | ❌ 발생하지 않음                          |
| 렌더링에 영향 없음       | ❌ 비효율적         | ✅ 적절                                   |
| 사용 목적                | UI에 영향을 주는 상태 | 내부 플래그, DOM 참조 등 렌더링과 무관한 값 |

---

## 💡 실무 팁

- 리렌더링이 필요 없는 값은 `useRef`를 사용하자!
- `ref`는 단순히 DOM에 접근하기 위한 용도뿐 아니라,  
  렌더링과 무관하게 **값을 유지하는 메모리 박스**처럼 활용할 수 있다.
