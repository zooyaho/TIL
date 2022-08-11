22.08.09 - TIL

> [참고] Udemy - React 완벽 가이드 with Redux, Next.js, TypeScript

# Potals & Refs

- 모달은 페이지 위에 표시되는 전체 페이지에 대한 오버레이다.
- 모달부분이 다른 요소에 중첩되어 작성할 경우 스타일링이나 접근성의 관점에서 좋지않다.
- 예를들어 스크린 리더 같은 경우에 렌더링 되는 HTML코드를 해석할때 일반적인 오버레이라고 인식하지 못할 수 있음!
- 일반적인 모든 종류의 오버레이나 관련 컴포넌트는 의미적인 관점이나 구조적인 관점으로 Real DOM 상에서 최상단에 작성되어야함.
- potals을 사용하여 이것을 구현할 수 있다!
  ![](https://velog.velcdn.com/images/zooyaho/post/a3caca3e-27ff-481f-85e6-41b963fa9167/image.png)
  👉🏻 사진과 같이 Potals을 사용하여 Error모달창 부분을 root div위(body의 직계자식)으로 만들 수 있다.

## 1. Potals

- 렌더링된 HTML 내용을 다른 곳으로 옮기는 것

1️⃣ 컴포넌트를 이동시킬 장소가 필요

```html
<!-- index.html -->
<body>
  <div id="backdrop-root"></div>
  <div id="overlay-root"></div>
  <div id="root"></div>
</body>
```

2️⃣ 컴포넌트에게 그 곳에 포털을 가져야 한다고 알림

- Backdrop과 modal부분을 컴포넌트로 나눈다!
- createPotal()메서드 사용

### ● ReactDOM.createPotal()

> - react-dom 라이브러리의 createPotal(렌더링이 되어야하는 리액트 노드, 포인터)

- 첫번째 인수: 렌더링이 되어야하는 리액트 노드를 jsx로 작성.
- 두번째 인수: 포인터, 이 요소가 렌더링 되어야 하는 실제 DOM의 컨테이너를 가리키는 포인터

```js
// ErrorModal.js
import ReactDOM from 'react-dom';
...
const Backdrop = props => {
  return <div className={classes.backdrop} onClick={props.onConfirm} />;
}
const ModalOverlay = props => {
  return (
    <Card className={classes.modal}>
      <header className={classes.header}>
        <h2>{props.title}</h2>
      </header>
      <div className={classes.content}>
        <p>{props.message}</p>
      </div>
      <footer className={classes.actions}>
        <Button onClick={props.onConfirm}>Okay</Button>
      </footer>
    </Card>
  );
}
...
const ErrorModal = (props) => {
return(
  <React.Fragment>
    {ReactDOM.createPortal(
     <Backdrop onConfirm={props.onConfirm} />,
     document.getElementById('backdrop-root')
    )}
    {ReactDOM.createPortal(
     <ModalOverlay
        title={props.title}
		message={props.message}
		onConfirm={props.onConfirm}
	 />,
     document.getElementById('overlay-root')
    )}
  </React.Fragment>
);
}
```

## 2. Refs

- 함수형 컴포넌트 안에서만 사용 가능.
- `useRef(초기값)`: 객체를 반환하는데, 안에 설정한 DOM노드로 값을 갖는 current프롭이 항상 있음.
- 다른 DOM요소에 접근해서 그것들을 작업할 수 있게함.
- 하지만 dom의 요소들을 변경하는 것은 지양해야한다. 보통은 값을 빠르게 읽어오고 싶을 때 사용함!
- 변수이름.current.(실제 돔에서 사용되는 속성) 으로 접근 가능

1️⃣ import

```js
import { useRef } from "react";
```

2️⃣ ref로 받아올 변수 선언

```js
const nameInputRef = useRef();
```

3️⃣ ref프롭으로 dom 요소 연결

```js
<input ref={nameInputRef} />
```

4️⃣ current프롭 사용

```js
console.log(nameInputRef.current.value);
```

🔥 주의사항 🔥

- DOM은 리액트에서만 조작되어야하기 때문에 Ref를 사용하는 것은 지양해야함.
- 이렇게 값만 읽을때는 state보다는 ref를 사용하여 코드를 줄이면 좋음.
- 입력요소에서 ref를 사용하면 '제어되지 않는 컴포넌트'라고 할수 있음. -> 리액트가 dom을 제어하지 못 하기 때문.

## ⭐️ 제어되는 컴포넌트와 제어되지 않는 컴포넌트

- ref로 읽어온 값을 이용하는 것은 리액트로 조작하는 것이 아닌 직접 DOM을 조작하는 것이기 때문에 **제어되지 않는 컴포넌트**라고 함
- 보통 폼요소는 자체적인 state가 있고, input요소를 state등으로 제어하는 것을 **제어되는 컴포넌트**라고 함

## 3. Forward Refs

- 어떤 state를 전달해서 그 컴포넌트에서 무언가를 변경하는 방식이 아니라 컴포넌트 내부에서 함수를 호출하는 방식, 일반적인 리액트 방식이 아니기 때문에 자주 사용하지 않는것이 좋음.
- 🔥 함수 컴포넌트(아래 예제에서 Input컴포넌트)는 ref를 받을 수 없기 때문에 오류가 발생함!

### ● React.forwardRef((props,ref)=>{})

: **사용자 지정 컴포넌트**에서 ref={}프롭을 사용하기 위해서는 해당 컴포넌트로 가서 컴포넌트함수를 React.forwardRef()로 감싸야 함!!, 사용할 요소에 ref={ref}를 추가하면, 이제 ref프롭을 사용할 수 있음.

#### 🚨 Error

👾 Input.js - 자식 컴포넌트

```js
const Input = (props) => {
  const inputRef = useRef();

  const activate = () => {
    // 제대로 focus되지 않음.
    inputRef.current.focus();
  };

  return (
    <div
      className={`${classes.control} ${
        props.isValid === false ? classes.invalid : ""
      }`}
    >
      <label htmlFor={props.id}>{props.label}</label>
      <input
        // ref 등록
        ref={inputRef}
        type={props.type}
        id={props.id}
        value={props.value}
        onChange={props.onChange}
        onBlur={props.onBlur}
      />
    </div>
  );
};
```

👾 Login.js - 부모 컴포넌트

```js
...
const submitHandler = (event) => {
    event.preventDefault();
    if (formIsValid) {
      authCtx.onLogin(emailState.value, passwordState.value);
    } else if (!emailIsValid) {
      // Input컴포넌트의 activate()호출 => error
      emailInputRef.current.activate();
    } else {
      // Input컴포넌트의 activate()호출 => error
      passwordInputRef.current.activate();
    }
  };

return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>
        <Input
		  // ref 등록
          ref={emailInputRef}
          id="email"
          label="E-Mail"
          type="email"
          isValid={emailIsValid}
          value={emailState.value}
          onChange={emailChangeHandler}
          onBlur={validateEmailHandler}
        />
        <Input
		  // ref 등록
          ref={passwordInputRef}
          id="password"
          label="Password"
          type="password"
          isValid={passwordIsValid}
          value={passwordState.value}
          onChange={passwordChangeHandler}
          onBlur={validatePasswordHandler}
        />
        <div className={classes.actions}>
          <Button type="submit" className={classes.btn}>
            Login
          </Button>
        </div>
      </form>
    </Card>
  );
```

👉🏻 Login.js의 Input컴포넌트에서 `ref={passwordInputRef}`와 같이 ref프롭으로 넘겨준 값을 Input.js의 `<input ref={inputRef} .../>`를 등록하여도 🔥 함수 컴포넌트(Input컴포넌트)는 ref를 받을 수 없기 때문에 오류가 발생함!

#### 💡 적용

1️⃣ **useImperativeHandle()훅 사용**

- 부모 컴포넌트의 state를 통해 컴포넌트를 제어하지 않고 프로그래밍적으로 **컴포넌트에서 무언가를 직접 호출하거나 조작해서 사용하게 해줌.**
- `useImperativeHandle( ref, ()=>{ return ({}); } )`
  - 첫번째 인수: ref
  - 두번째 인수: 객체를 반환하는 함수, 객체는 외부에서 사용할 수 있는 모든 데이터를 포함함. 이름을 통해 접근할 수 있어야 하는것을 가리킴.
- `React.forwardRef((props, ref)=>{});`
  - 컴포넌트는 두번째 인자로 ref를 받아올 수 있음.
  - 부모 컴포넌트에서 ref프롭으로 등록하면 자식 컴포넌트에서 ref를 받아올 수 있음.
  - **두번째 인자로 받아온 ref를 활성화 시키려면 React.forwardRef()메서드로 해당 컴포넌트를 감싸야 함!!**

🔥 이 훅 사용은 최대한 피하는 것이 좋지만 예제와 같이 focus나 스크롤같은 기능에 적용될 수 있음!

👾 Input.js - 자식 컴포넌트

```js
import React, { useRef, useImperativeHandle } from "react";

const Input = React.forwardRef((props, ref) => {
  const inputRef = useRef();

  const activate = () => {
    inputRef.current.focus();
  };

  useImperativeHandle(ref, () => {
    return {
      // focus는 위 activate함수 식별자를 참조함!!
      // focus는 외부(부모컴포넌트 사이)에서 사용가능!
      focus: activate,
    };
  });

  return (
    <div
      className={`${classes.control} ${
        props.isValid === false ? classes.invalid : ""
      }`}
    >
      <label htmlFor={props.id}>{props.label}</label>
      <input
        // ref 등록
        ref={inputRef}
        type={props.type}
        id={props.id}
        value={props.value}
        onChange={props.onChange}
        onBlur={props.onBlur}
      />
    </div>
  );
});
```

👾 Login.js - 부모 컴포넌트

```js
...
const submitHandler = (event) => {
    event.preventDefault();
    if (formIsValid) {
      authCtx.onLogin(emailState.value, passwordState.value);
    } else if (!emailIsValid) {
      // 자식 컴포넌트(Input)의 focus함수를 실행
      emailInputRef.current.focus();
    } else {
      passwordInputRef.current.focus();
    }
  };
```

👉🏻 Input컴포넌트의 useImperativeHandle()에 추가한 focus함수는 외부(Login.js)에서 사용할 수 있기 때문에 active()함수가 실행되어 유효하지 않는 input에 focus가 적용된다!!

> [참고] Udemy - React 완벽 가이드 with Redux, Next.js, TypeScript
