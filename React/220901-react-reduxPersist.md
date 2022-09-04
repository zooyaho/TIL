# ⚛️ redux-persist

- redux store의 데이터와 storage를 sync 해주는 라이브러리
- cookie, local storage, session storage 등 다양하게 지원
- store 중 일부 reducer를 선택해 저장할 수도 있으며 property 단위로 선택할 수 있도록 whitelist, blacklist 지원한다.
- store가 업데이트 될때마다 자동으로 localStorage등에 업데이트 됨.
- 리덕스 초기화 시 localStorage에서 값을 가져와 initial state에 값을 할당 해줌!!

- **useReducer가 반환하는 update된 state들을 자동으로 localStorage에 저장해줌!**

```js
npm i redux-persist

import {persistStore, persistReducer} from 'redux-persist';
import storage from 'redux-persist/lib/storage' // defaults to localStorage for web

const persistConfig = {
  key:root,
  storage
}

const rootReducer = combineReducers({
  user: userReducer,
  counter: conterReducer
});

const persistedReducer = persistReducer(persistConfig, rootReducer);

const store = createStore(
  persistedReducer,
  applyMiddleware(
    someMiddleware
  )
);

const persistor = persistStore(store);

...
// index.js
import {PersistGate} from 'redux-persist/integration/react';
...
root.render(
  <Provider store={store}>
    <PersistGate loading={null} persistor={persistor}>
      <App />
    </PersistGate>
  </Provider>
);
```

👾 한개의 리듀서만 localStorage에 저장할 경우

```js
const rootReducer = combineReducers({
  user: persistReducer(persistConfig,userReducer)
  counter: conterReducer
});

// const persistedReducer = persistReducer(persistConfig, rootReducer);

const store = createStore(
  // persistedReducer를 rootReducer로 변경
  rootReducer,
  applyMiddleware(
    someMiddleware
  )
);
```

👉🏻 localStorage에 저장하고 싶은 리듀서만 persistReducer로 감싸면 된다.

### whitelist, blacklist

: 하나의 리듀서에서 localStorage에 저장할 프로퍼티들을 개별적으로 지정가능함.

```js
const persistConfig = {
  key: "user persist의 유니크한 key값 지정",
  storage,
  whitelist: ["name"],
  // localStorage에 저장할 프로퍼티들을 지정
};
```
