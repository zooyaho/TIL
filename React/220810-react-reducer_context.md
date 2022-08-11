22.08.10 - TIL

> [참고] Udemy - React 완벽 가이드 with Redux, Next.js, TypeScript

# ⚛︎ useReducer & useContext

## 1. useReducer()

- **여러 state들이 함께 속해 있는 경우, 같은 것을 관리함.**
- 관리하는 측면이 다를뿐, 여러 state가 같이 바뀌거나 서로 관련된 경우, useState나 거기에서 얻은 state는 종종 사용 및 관리가 어려워지거나 오류 발생이 쉬움.

### ● const [state, dispatchFn] = useReducer(reduceFn, initialState, initFn)

- `state`: 최신 state 스냅샷
- `dispatchFn` : state 스냅샷을 업데이트하는 함수, 액션을 디스패치함
- `reducerFn`:  
  📎 `(prevState, action) => newState`
  - 첫번째 인수: 최신 state 스냅샷
  - 두번째 인수: 디스패치된 액션(리액트는 새 액션이 디스패치될 때마다 이 리듀서함수를 호출하기 때문)
  - **새로운 업데이트된 state를 반환함**
- `initialState` : 초기 state 설정
- `initFn` : 초기 함수를 설정

### ● 예시

#### 👾#01 - useReducer 적용 전

```js
const [enteredEmail, setEnteredEmail] = useState("");
const [emailIsValid, setEmailIsValid] = useState();
const [enteredPassword, setEnteredPassword] = useState("");
const [passwordIsValid, setPasswordIsValid] = useState();
const [formIsValid, setFormIsValid] = useState(false);
```

👉🏻 email요소는 enteredEmail, emailIsValid 두개의 state를 가지고 있음. 따라서 함께 관리할 수 있음!

```js
const emailChangeHandler = (event) => {
  // 변경된 email값 state에 저장
  setEnteredEmail(event.target.value);

  // email 요소의 값이 변경될 때마다 email, psw 유효성 검사 실시
  setFormIsValid(
    event.target.value.includes("@") && enteredPassword.trim().length > 6
  );
};
// email이 blur될 경우 유효성검사에 따른 class속성 추가
const validateEmailHandler = () => {
  setEmailIsValid(enteredEmail.includes("@"));
};
```

👉🏻 react가 state를 업데이트하는 스케쥴링 방식 때문에, 가장 최근의 email이나 paw로 유효성 검사를 실시하지 않을 수 있음.  
👉🏻 **이처럼 다른 state를 기반으로 하는 state를 업데이트하는 경우 하나의 state로 병합하는 것이 좋음!!**

```js
return ( ...
<div className={ `${classes.control} ${ emailIsValid === false ? classes.invalid : ''}` }>
  <label htmlFor="email">E-Mail</label>
  <input
    type="email"
    id="email"
    value={enteredEmail}
    onChange={emailChangeHandler}
    onBlur={validateEmailHandler}
  />
</div>
...
<div className={classes.actions}>
// formIsValid에 따른 로그인 버튼 활성화/비활성화
  <Button type="submit" className={classes.btn} disabled={!formIsValid}>Login</Button>
</div>
);
```

#### 👾#02 - useReducer 적용

```js
// Login컴포넌트 밖에서 선언
const emailReducer = (state, action) => {
  // 리듀서 함수 내부에서는 컴포넌트 함수 내부에서 만들어진 어떤 데이터도 필요하지 않기 때문!!!
  // 리듀서 함수 내부에서 요청되고 사용되는 모든 데이터는 리액트가 이 함수를 실행할 때 자동으로 이 함수(emailReducer)로 전달이 됨.
  if( action.type === 'USER_INPUT'){
    // dispatchEmail함수가 실행하도록 트리거 조건문 생성
    return { value: action.val, isValid: action.val.includes('@') };
  }
  if( action.type === 'INPUT_BLUR'){
    // 입력 도중 input이 blur처리 되었을 때 입력된 값을 잃지 않기위해 최신의 state 스냅샷을 사용(첫번째 인수인 state사용)
    return { value: state.value, isValid: state.value.includes('@') };
  }
  // 기본 state값 지정
  return { value: '', isValid: false };
}

const Login = () => {
// const [enteredEmail, setEnteredEmail] = useState('');
// const [emailIsValid, setEmailIsValid] = useState();
const [emailState, dispatchEmail] = useReducer(emailReducer, { value: '', isValid: null });
  // isValid의 초기값을 null또는 undefined로 해야 처음에 blur처리가 되지 않음!
...
}
```

👉🏻 **dispatchEmail에서 생성한 action 값이 emailReducer함수의 두번째 인수로 들어옴!!!**  
👉🏻 첫번째 인수로는 최신 상태의 state값이 들어옴!!

```js
const emailChangeHandler = (event) => {
  // 업데이트하는 경우 디스패치 함수 사용
  // setEnteredEmail(event.target.value);
  dispatchEmail({ type: "USER_INPUT", val: event.target.value });

  // enteredEmail => emailState.value
  setFormIsValid(
    emailState.value.includes("@") && passwordState.value.trim().length > 6
  );
};

const validateEmailHandler = () => {
  // enteredEmail.includes('@') => emailState.isValid
  // input이 blur되었는지 확인하는 변수이므로 값(value)는 필요 없음
  dispatchEmail({ type: "INPUT_BLUR" });
};
```

👉🏻 **업데이트하는 경우 디스패치 함수 사용**  
👉🏻 dispatchEmail({type: 'USER_INPUT', val: event.target.value}) : **인수로 action작성**, 보통 객체 사용(필드를 가짐 -> 대부분 명명된 타입, 페이로드 추가(option))  
📎 **페이로드(payload)는 전송되는 데이터를 의미**

```js
return ( ...
// emailIsValid => emailState.isValid
<div className={ `${classes.control} ${ emailState.isValid === false ? classes.invalid : ''}` }>
  <label htmlFor="email">E-Mail</label>
  <input
    type="email"
    id="email"
// enteredEmail => emailState.value
    value={enteredEmail}
    onChange={emailChangeHandler}
    onBlur={validateEmailHandler}
  />
</div>
...
<div className={classes.actions}>
// formIsValid에 따른 로그인 버튼 활성화/비활성화
  <Button type="submit" className={classes.btn} disabled={!formIsValid}>Login</Button>
</div>
);
```

## 2. React Context(Context API)

- 프롭을 통해 많은 컴포넌트를 거쳐 많은 데이터를 전달할 때 일어남.
- **리액트에 내장된, 내부적인 state저장소**
- 앱의 어떤 컴포넌트에서라도 **직접 변경**하여, 앱의 다른 컴포넌트에 직접 전달할 수 있게 해줌. **프롭체인을 구축하지 않아도 됨.**
- 전체 state관리하는 **폴더명**으로 보통 **store, context, state**라고 지음. 케팝표기법을 사용하여 파일명 지음.(auth-context 등)
- context파일명은: 소문자-context EX) cart-context

### 1️⃣ context객체 생성

```js
// store/auth-context.js
import React from "react";

const AuthContext = React.createContext({
  isLoggedIn: false,
  onLogout: () => {},
});

export default AuthContext;
```

👉🏻 createContext()인수로 보통 객체를 사용하여 state를 지정, AuthContext에는 state객체를 반환한 값이 저장됨.(AuthContext는 현재 객체임.)
👉🏻 onLogout에 더미 함수를 지정: IDE자동 완성을 더 좋게 하기 위함.

### 2️⃣ 앱에서 컨텍스트를 사용하려면

#### ① 공급(provide)

- 리액트에 생성한 context를 알려야함.(=공급)
- 해당 context를 사용하려는 컴포넌트들에게 접근 권한을 줌.(=jsx코드로 감쌈, 따라서 컴포넌트들은 해당 context를 리스닝할 수 있게됨.)
- 기본값이 있으면 공급자는 필요없음!

```js
// App.js
import AuthContext from './store/auth-context.js';
...
return(
  <AuthContext.provider>
    <MainHeader />
  </AuthContext.provider>
);
```

👉🏻 AuthContext객체의 provider속성을 적용함으로써 <AuthContext.provider>는 컴포넌트가 되어 jsx코드로 사용할 수 있게됨!  
👉🏻 MainHeader의 하위 컴포넌트까지 해당 context를 사용할 수 있음!

#### ② 소비: 연동, 리스닝

📎 **리스닝하는 2가지 방법**

##### 1) **소비자(Consumer)**로 사용

- **자식으로 함수를 갖으며, 인수로 컨텍스트 데이터를 가져오고 jsx코드를 반환해야함!!**
- 🔥 **context에 기본값이 있을경우에는 공급자가 필요없음!!** 하지만 공급자를 사용하는 패턴이 중요하기 때문에 공급자를 사용하는데, context기본값이 있고 공급자를 사용할 경우 **충돌이 남**
- 이 충돌은 **공급자에서 value프롭으로 기본값을 작성하면 해결 됨.**  
  -> 문자열이나 객체는 보낼수 없지만, **함수나 변수는 보낼수 있음!!**

👾 App.js

```js
...
const [isLoggedIn, setIsLoggedIn] = useState(false);
...
return(
  <AuthContext.provider value={{
    isLoggedIn: isLoggedIn,
    onLogout: logoutHandler
  }}>
    <MainHeader />
    <main>
  	  {!isLoggedIn && <Login onLogin={loginHandler} />}
  	  {isLoggedIn && <Home onLogin={loginHandler} />}
    </main>
  </AuthContext.provider>
);
...
```

👉🏻 value프롭을 추가하여 **변경된 새 값이 모든 소비 컴포넌트에 전달이 됨**  
👉🏻 isLoggedIn **state**가 변경될 때마다 리액트에 의해 업데이트가 되기때문에, context의 isLoggedIn도 값이 업데이트가되어 최신 값이 소비 컴포넌트에 전달이 된다.  
👉🏻 onLogout: logoutHandler // **함수포인터만 전달**하여 함수를 동적으로 context로 사용할 수 있음!!

👾 MainHeader.js

```js
...
...
return(
  <header>
    <Navigation
  	  //isLoggedIn={props.isAuthenticated}
    />
  </header>
);
...
```

👉🏻 context를 사용하여 prop으로 전달한 것을 중지할 수 있게됨!

👾 Navigation.js

```js
import AuthContext from '../store/auth-context.js';
...
return(
  <AuthContext.Consumer>
  {(ctx) => {
    return (
      // jsx코드 작성
      <nav>
      	<ul>
      	  /*
      	  {props.isLoggedIn && (
            <li><a href='/'>Users</a></li>
          )}
          */
      	  {ctx.isLoggedIn && (
            <li><a href='/'>Users</a></li>
          )}
      	  {ctx.isLoggedIn && (
            <li><a href='/'>Admin</a></li>
          )}
		  {ctx.isLoggedIn && (
            // onClick={props.onLogout}
            <li><button onClick={ctx.onLogout}>Logout</button></li>
          )}
        </ul>
      </nav>
    );
  }}
  </AuthContext.Consumer>
);
```

👉🏻 여기서 ctx(컨텍스트 데이터)는 객체이므로 프로퍼티(isLoggedIn)를 사용

##### 2) 리액트 훅 사용 - useContext()

`useContext()`

- 소비자보다 많이 사용하는 방법
- 컨텍스트를 활용하고 리스닝할 수 있게 해줌.
- **인수로 컨텍스트를 가리키는 포인터를 전달**

👾 Navigation.js

```js
import React, {useContext} from 'react';
import AuthContext from '../store/auth-context.js';
...
// context pointer 전달
const ctx = useContext(AuthContext);

return(
  <nav>
    <ul>
  	  {ctx.isLoggedIn && (
   		<li><a href='/'>Users</a></li>
   	  )}
  	  ...
    </ul>
  </nav>
);
```

### 📎 context객체 생성 파일에 공급자컴포넌트 추가 작성...!

- 💡 **전체 state를 별도의 공급자 컴포넌트에서 관리함**
- 독립 실행형 파일 생성!!

👾 store/auth-context.js

```js
import React from "react";

const AuthContext = React.createContext({
  isLoggedIn: false,
  onLogout: () => {},
  onLogin: (email, password) => {},
});

export const AuthContextProvider = (props) => {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  useEffect(() => {
    // 새로고침 시 로그인 상태 유지하는 이펙트!
    const storagedUserLoggedInInfomation = localStorage.getItem("isLoggedIn");

    if (storagedUserLoggedInInfomation === 1) {
      setIsLoggedIn(true);
    }
  }, []);

  const logoutHandler = () => {
    localStorage.removeItem("isLoggedIn");
    setIsLoggedIn(false);
  };
  const loginHandler = () => {
    localStorage.setItem("isLoggedIn", 1);
    setIsLoggedIn(true);
  };

  return (
    <AuthContext.provider
      value={{
        isLoggeIn: isLoggeIn,
        onLogin: loginHandler,
        onLogout: logoutHandler,
      }}
    >
      {props.children}
    </AuthContext.provider>
  );
};

export default AuthContext;
```

👾 index.js

```js
import {AuthContextProvider} from './store/auth-context.js'
...
root.render(
  <AuthContextProvider>
    <App />
  </AuthContextProvider>
)
...
```

👾 App.js

```js
import {useContext} from 'react';
import AuthContext from './store/auth-context.js';
...
const ctx = useContext(AuthContext);

return(
  <React.Fragment>
    <MainHeader />
    <main>
  	  {!ctx.isLoggedIn && <Login />}
  	  {ctx.isLoggedIn && <Home />}
    </main>
  </React.Fragment>
);
...
```

👉🏻 이렇게 리팩토링을 하면 App컴포넌트의 코드가 줄어들고, 기능을 분리하여 관리할수 있다는 장점이 있음!  
👉🏻 이것은 context를 관리하는 방법 중 하나임!

### 🧐 prop vs context

: 구성을 하기위해서는 프롭을 사용하고  
: **긴 프롭체인을 교체하거나, 컴포넌트 또는 전체 앱에서 state관리를 하려면** 컨텍스트를 사용함. 하지만 변경이 잦은 경우에는 적합하지 않음.  
: 그럼에도 불구하고(1초에 많이 state가 변경되는 경우) 컨텍스트를 사용하고 싶을 경우 "리덕스"를 사용하면 됨!!!
