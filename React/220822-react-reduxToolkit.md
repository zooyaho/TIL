22.08.22 - TIL

# ⚛️ React Toolkit

- 리덕스의 특징 몇 가지 특징을 단순화함.
- 훨씬 짧고 간결하며, 유지보수가 쉬워짐!

**📎 패키지 설치**

`npm install @reduxjs/toolkit`

- 패키지에 redux라이브러리가 포함되어있기 때문에, redux라이브러리를 별도로 설치하지 않아도 됨.

## 💡 함수

`import {...} from '@reduxjs/toolkit'`

- `createSlice({name: '', initialState: '', reducers: {}})`
  - 객체를 인수로 받음
  - 전역상태의 slice를 미리 만들어 놓음.
  - 서로 직접적인 관계가 아닌 상태가 여러개로 나눠져 있음.
  - **name** : 상태마다 식별자가 필요하기 때문에 모든 slice는 이름이 있어야함.
  - **initialState** : 상태마다 초기상태 설정
  - **reducers** : 객체 혹은 맵으로 리듀서 설정, reduders 에 정의한 **메서드들은 자동으로 첫번째인수로 state 최근값이 들어오고, 두번째 인수로 action이 들어옴!**, action에 따라 자동으로 메서드가 리덕스에 의해 호출 됨.
- `configureStore({reducer: ...})`
  - createStore()처럼 store를 만듬. **다른 점은 모든 리듀서를 하나의 큰 리듀서로 병합함!!**
  - 인수로 설정객체가 들어오며, 리듀서 프로퍼티를 정함.
  - 리듀서 프로퍼티에 **전역 상태를 담당하는 주요 리듀서로서 사용할 수 있음.**
  - `reducer가 한개일 경우` : ex) configureStore({reducer: counterSlice.reducer})
  - `reducer가 여러개일 경우` : **reducer map을 생성하여 리듀서마다 key값을 설정한다.** ex) configureStore({reducer :{counter: counterSlice.reducer}})
- `리듀서Slice.actions.메서드key`
  - actions객체의 key로 해당 리듀서 메서드를 사용할 수 있음.
  - type프러퍼티를 가지고 있는 **해당 액션 객체를 반환**
  - 그러므로 이런 메서드key를 액션 생성자라고 부름.

### ✔️ 예제

👾 store/index.js - toolkit사용 전

```js
const counterReducer = (state = initialState, action) => {
  if (action.type === "increment") {
    return {
      counter: state.counter + action.amount,
      showCounter: state.showCounter,
    };
  }

  if (action.type === "decrement") {
    return {
      counter: state.counter - action.amount,
      showCounter: state.showCounter,
    };
  }

  if (action.type === "toggle") {
    return {
      showCounter: !state.showCounter,
      counter: state.counter,
    };
  }

  return state;
};

const store = createStore(counterReducer);

export default store;
```

👾 store/index.js - **toolkit 사용**

```js
import { createSlice } from "@reduxjs/toolkit";

const initialState = { counter: 0, showCounter: true };

const counterSlice = createSlice({
  name: "counter", // 식별자 설정
  initialState: initialState, // 초기상태 설정
  reduders: {
    // 리듀서 설정
    increment(state, action) {
      state.counter = state.counter + action.payload;
    },
    decrement(state, action) {
      state.counter = state.counter - action.payload;
    },
    toggleCounter(state) {
      state.showCounter = !state.showCounter;
    },
  },
});
// action에 따라 자동으로 메서드가 리덕스에 의해 호출 됨.

const store = configureStore({ counter: counterSlice.reducer });

// 액션 식별자 값을 얻으려면 counterSlice.actions 사용
// 액션이 필요한 컴포넌트에서 사용 가능하게 export 함!
export const counterAcrions = counterSlice.actions;

export default store;
```

🔥 `increment(state, action) { state.counter = state.counter + action.amount; },`
👉🏻 여전히 원래 있는 상태를 변경할 수 없지만, React Toolkit에서 제공하는 **createSlice를 사용하면 기존상태를 직접적으로 변경할 수 있음.**
👉🏻 왜냐하면 React Toolkit 내부적으로 immer라는 다른 패키지를 사용하는데 **이런 코드를 감지하고 자동으로 원래 있는 상태를 복제하고 새로운 상태 객체를 생성하여 오버라이드를 하기 때문이다.**
👉🏻 createSlice()를 사용하면 불변성을 고려하지 않고 직접 상태 업데이트할 수 있다. -> 내부적으로 알아서 변경할 수 없는 코드로 변경함.

👾 Counter.js

```js
import { useSelector, useDispatch } from "react-redux";
import { counterActions } from "../store/index";

const Counter = () => {
  const dispatch = useDispatch();
  const counter = useSelector((state) => state.counter);
  const show = useSelector((state) => state.showCounter);

  const incrementHandler = () => {
    // action객체가 자동 생성 후 increment메서드 호출
    // payload가 있을 경우 인자로 전달
    dispatch(counterActions.increment(10));
    // Toolkit이 { type: SOME_UNIQUE_IDNETIFIER, payload: 10}을 생성하여 자동으로 전달!!
    // 이때 payload는 기본값으로 사용하는 필드임!!
  };

  const decrementHandler = () => {
    dispatch(counterActions.decrement(10));
  };

  const toggleCounterHandler = () => {
    dispatch(counterActions.toggleCounter());
  };

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      {show && <div className={classes.value}>{counter}</div>}
      <div>
        <button onClick={incrementHandler}>Increment</button>
        <button onClick={increaseHandler}>Increase by 10</button>
        <button onClick={decrementHandler}>Decrement</button>
      </div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

export default Counter;
```
