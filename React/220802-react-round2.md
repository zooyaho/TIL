220802 - TIL

- fastcampus Megabyte School : 프론트엔드 개발 취업 연계 과정 강의에서 복습한 내용을 간단하게 정리 하였습니다.

# [리액트 2회차]

## 1. 이벤트 함수에서 고차함수를 사용하여 매개변수 전달

![](https://velog.velcdn.com/images/zooyaho/post/53ec3628-53e0-4836-b87c-e1d804a88b16/image.png)

## 2. state

> 컴포넌트안에서 관리되는 유동적인 데이터

```js
const [value, setValue] = useState(initial value);
```

- `useState()`는 `배열`을 반환함. 위 예시는 `구조분해 할당`을 사용
- state값 변경으로 setValue()함수를 사용하는데, 인자로 변경할 값을 지정함. `ex) setValue(3);`

## 3. React component가 다시 render되는 조건

1. prop, state가 업데이트 된 경우
2. 부모 컴포넌트가 다시 렌더된 경우에 자식 컴포넌트도 리렌더 발생함

🔥 업데이트 시 주의사항

> 참조타입 은 참조(주소)가 바뀌어야 업데이트 된걸로 인식됨. 값이 바뀐다고 업데이트 되는것이 아님!!

## 4. state 업데이트의 비동기성

- 코드가 동기적으로 실행되어도 state값 업데이트를 기다리지 않고 다음라인을 진행하여 한번만 업데이트 됨.
  ![](https://velog.velcdn.com/images/zooyaho/post/cd922198-b9d8-4b65-b62e-4ad0bc26d899/image.png)

### 💡 state가 비동기적으로 업데이트 되는 이유

👉🏻 한 컴포넌트에 여러개의 state가 있고, 이 모든 state들을 한번에 변경할 경우,<br> 컴포넌트는 state가 업데이트 되는 개수만큼 렌더링이 되는것이 아닌,<br> 모두 업데이트 후에 한번만 렌더링이 됨!! 이 때문에 비동기적인 update를 함!!

### 💡 해결방법

: setter함수의 인자로 함수를 넘겨줌!!
`ex) setNum( (prev) => prev + 1 );`
: setter함수의 인자로 함수 는 첫번째 인자로 prev값, 두번째 인자로 cur값이 들어옴~!
