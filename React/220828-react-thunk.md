TIL - 220828

# ● Thunk를 이용해 비동기 작업을 처리하는 방법

## Thunk

- 다른 작업이 완료될 때까지 작업을 지연시키는 함수이다.
- 비동기 작업을 처리 시 유용함.
- 작업을 반환하는 다른 함수를 반환한다...
- toolkit에 내장되어있음.

## createAsyncThunk

- **비동기 작업을 처리하는 action을 만들어 준다!**
- action creator 생성
- `createAsyncThunk('type', async ()=>{})`
  - 첫번째 인자: action creator이기 때문에 **type을 지정**
  - 두번째 인자: action이 실행됐을 때 처리되어야하는 작업

## 📎 예제

: 버튼 클릭 시 api호출하여 data를 store에 저장

🔴 thunk사용 전

```js
<button
  onClick={async () => {
    const response = await fetch("https://...");
    const data = response.json();
    dispatch(set(data.value));
    // dispatch({type:'counterSlice/set', payload: data.value});
  }}
>
  async fetch
</button>
```

👉🏻 api호출이 중복 될 경우, 코드양이 길어지고 가독성이 떨어짐

🔵 thunk사용 후

👾 App.js

```js
import {asyncUpFetch} from './counterSlice';
...

<button onClick={()=>{
  dispatch(asyncUpFetch());
}}>async thunk</button>
```

👾 CounterSlice.js

```js
const asyncUpFetch = createAsyncThunk(
  "counterSlice/asyncUpFetch", // action 타입 지정
  async () => {
    const resp = await fetch("htttps://...");
    const data = resp.json();
    return data.value;
  }
);

const counterSlice = createSlice({
  name: "counterSlice",
  initialState: { value: 0, status: "welcome" },
  // 동기적인 작업은 reducers에서 작업
  reducers: {
    up: (state, action) => {
      state.value = state.value + action.payload;
    },
  },
  // 비동기적인 작업은 extraReducers에서 작업
  extraReducers: (builder) => {
    // pending일때 실행할 reduser를 두번째 파라미터에 작성
    builder.addCase(asyncUpfetch.pending, (state, action) => {
      state.status = "Loading";
    });
    builder.addCase(asyncUpfetch.fulfilled, (state, action) => {
      state.status = "Complete";
    });
    builder.addCase(asyncUpfetch.rejected, (state, action) => {
      state.status = "Fail";
    });
  },
});
```

👉🏻 동기적인 작업은 reducers에서 작업하며, **자동으로 type이 생성됨!**  
👉🏻 비동기적인 작업은 extraReducers에서 작업하며, **자동으로 type이 생성되지 않음!**

![](https://velog.velcdn.com/images/zooyaho/post/bb9b0197-f737-45d8-b2bf-d6669747d4be/image.png)

> 참고 생활코딩
