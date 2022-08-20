# ⚛️ Redux

> - 리덕스는 크로스 컴포넌트 상태, 앱와이드 상태를 위한 **상태 관리 시스템**이다.
> - 일반적인 프로그래밍 개념으로 **리듀서 함수**는 입력을 변환해서 새로운 출력, 새로운 결과를 뱉어내는 기능을 수행한다.

1. **로컬상태** : 데이터가 변경되어서 하나의 컴포넌트에 속하는 UI에 영향을 미치는 상태를 말한다.
2. **크로스 컴포넌트 상태** : 모달 오버레이 컴포넌트는 다수의 컴포넌트에서 사용하게 되는데, 이때 모달을 닫는 트리거는 모달 컴포넌트에서 여는 트리거는 다수의 컴포넌트에서 관리를 하게 된다. 이런 경우와 같이, 다수의 컴포넌트가 협력하여 상태관리를 하는 상태를 말한다.
3. **앱와이드 상태** : 애플리케이션의 모든 컴포넌트에 영향을 미치는 상태를 말한다. 예를들어 로그인 상태에 따른 UI변경

## ● React Context의 잠재적 단점

- 리액트 컨텍스트를 사용하면 설정과 상태관리가 복잡해질 수 있다.
- 데이터가 자주 변경되는 경우 성능 저하의 원인이 될 수 있다.
  ![](https://velog.velcdn.com/images/zooyaho/post/015d152a-f434-4135-b59d-1786b390269f/image.png)

## ● Redux 작동 방식

- 하나의 **중앙 데이터 저장소**, 전체 어플리케이션의 모든 상태를 저장함.
- 중앙 데이터 저장소에 **구독**을 하여 데이터를 사용한다.
- 절대로 저장된 데이터를 직접 조작하지 않으며, 데이터 변경(update)은 변형을 담당하는 리듀서 함수를 설정하여 사용한다.
- 컴포넌트가 액션을 발송하여 그에 따른 리듀서함수를 실행(트리거)한다.

#### 📎 설치

`npm install redux` : redux는 react와 연관없는 패키지이다.
`npm install react-redux` : redux와 react앱의 작업을 쉽게 하기위해 해당 패키지 설치.

- 리덕스 스토어에 컴포넌트를 서브스크라이브라고 하며

### ✔️ Reducer 함수

- **리듀서 함수는 리덕스 라이브러리에 의해 호출이 됨.**
- **항상 새로운 상태 객체를 리턴**해야 함.
- 리듀서 함수는 **순수한 함수**여야함. 여기서 순수한 함수란 리덕스가 제공하는 입력을 취하고 예상된 출력물인 **새로운 상태 객체를 생성**해야 한다.
- 함수 안에서는 어떠한 부수적인 효과가 없어야 하는데, 예를들어 HTTP요청을 전송한다거나 뭔가를 로컬 저장소에 기록한다거나 로컬 저장소에서 뭔가를 가져오지 말아야 함.

### ✔️ 메서드

- `subscribe()` : 인자로 구독자 함수를(저장소를 사용하는 컴포넌트) 취함. **리덕스는 데이터와 저장소가 변경될 때마다 인자로 받은 함수(구독자 함수)를 실행시킴.**
- `getState()` : createStore()로 생성된 저장소에서 사용 가능한 메서드, **업데이트된 저장소 데이터를 반환**함.
- `dispatch()` : 액션을 발송하는 메소드, 액션은 객체타입  
  💡 action을 dispatch하면 리듀서 함수에서 **해당 type의 조건문이 없어도 리듀서 함수는 실행된다!!**

### ✔️ 예제

👾 redux-demo.js

```js
// js에서 서드파티패키지를 inport하는 방법
const redux = require("redux");

const counterReducer = (state = { counter: 0 }, action) => {
  if (action.type === "increment") {
    return {
      counter: state.counter + 1,
    };
  }
  if (action.type === "decrement") {
    return {
      counter: state.counter - 1,
    };
  }
  // 초기값 state 반환
  return state;
};

const store = redux.createStore(counterReducer);

/* 구독자 컴포넌트 */
const counterSubscriber = () => {
  // 저장소에 있는 업데이트된 데이터 반환
  const latestState = store.getState();
  console.log(latestState);
};

// subscribe()메서드는 인자로 counterSubscriber함수를 취함.
// 리덕스는 데이터와 저장소가 변경될 때마다 인자로 받은 함수(구독자 함수)를 실행시킴.
store.subscribe(counterSubscriber);

// dispatch()는 액션을 발송하는 메소드, 액션은 객체타입
// 💡 action을 dispatch하면 리듀서 함수에서 해당 type의 조건문이 없어도 리듀서 함수는 실행된다!!
store.dispatch({ type: "increment" });
store.dispatch({ type: "decrement" });
```

**💡 파라미터 state에 초기값을 설정하는 이유**

- **저장소를 초기화할 때 리덕스가 리듀서함수를 실행시키기 때문**에 state에 보조 기본값(=초기값)을 지정해야 함.

**💡 파라미터로 함수의 포인터를 전달하는 이유는 리듀서와 구독함수를 모두 리덕스가 실행하기 때문**
