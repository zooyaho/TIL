22.08.18 - TIL

# ⚛️ 양식 및 사용자 입력 방법

## 1. 사용자 입력 값 가져오기

```js
import { useState, useRef } from "react";

const SimpleInput = (props) => {
  const [enteredName, setEnteredName] = useState("");
  const nameInputRef = useRef();

  const nameInputChangeHandler = (event) => {
    setEnteredName(event.target.value);
  };

  const formSubmitHandler = (event) => {
    evnet.preventDefault();
    console.log(enteredName);

    const enteredValue = nameInputRef.current.value;
    console.log(enteredValue);

    // input 초기화
    setEnteredInput("");
  };

  return (
    <form>
      <div onSubmit={formSubmitHandler}>
        <label htmlFor="name">Your Name</label>
        <input
          type="text"
          id="name"
          onChange={nameInputChangeHandler}
          ref={nameInputRef}
          value={enteredName}
        />
      </div>
      <div>
        <button>Submit</button>
      </div>
    </form>
  );
};
```

1️⃣ useState()훅과 onChange()함수를 사용한 방법

- **즉각적인 유효성 검증을 위해 키 입력마다 입력값이 필요할 경우 사용**
- **입력된 값을 초기화 하기 위해 사용** (setEnteredInput(''))

2️⃣ useRef()훅을 사용한 방법

- **폼이 제출되었을 때 한번만 필요할 경우 사용**
- `nameInputRef.current.value = '';` : ref를 사용하여 초기화 하면, DOM을 직접 조작하기 때문에 바람직하지 않음.

## 2. 검증 에러 피드백 제공하기

### 1️⃣ input이 빈값일 때 submit버튼 클릭 시 유효성 에러 처리

```js
const SimpleInput = (props) => {
  ...
  const [enteredNameIsValid, setEnteredNameIsValid] = useState(false);
  const [enteredNameTouched, setEnteredNameTouched] = useState(false);

  useEffect(() => {
    if (enteredNameIsValid) {
      console.log('Name Input is valid!');
    }
  }, [enteredNameIsValid]);
  ...

  const formSubmitHandler = (event) => {
    ...
    // submit버튼을 클릭 했을 때 true로 변경
    setEnteredNameTouched(true);

    if(enteredName.trim() === ''){
      // 공백이면 유요함
      setEnteredNameIsValid(false);
      return;
    }
    // 공백이 아니면 유효함
    setEnteredNameIsValid(true);
    ...
  }

  // input 값이 유효하지 않을 때 error출력
  // 초기화 화면 시에는 input이 빈값이여도 error가 출력되지 않음!
  const nameInputIsInvalid = !enteredNameIsValid && enteredNameTouched;

  return (
    <form onSubmit={formSubmitHandler}>
      <div>
        <label htmlFor='name'>Your Name</label>
        <input
		  type='text'
		  id='name'
		  onChange={nameInputChangeHandler}
		  ref={nameInputRef}
		  value={enteredInput}
  		/>
        // 유효성 error 처리
        {nameInputIsInvalid && (
          <p>Name must not be empty.</p>
        )}
      </div>
      <div>
        <button>Submit</button>
      </div>
    </form>
  );
};
```

👉🏻 초기화 화면 시에는 input이 빈값이여도 error가 출력되지 않음!
👉🏻 **enteredNameIsValid만 사용하여 유효성 검사할 경우**

- 초기화 화면에서 유효성 error를 출력하지 않기 위해 임시방편으로 enteredNameIsValid의 초기값을 true로 설정하게 된다.
- 만약 useEffect훅에서 해당 **name input의 유효성에 따른 api호출 등을 할 경우 초기값이 true이기 때문에 빈값을 가지고 api호출을 하게 된다!**
- 그래서 enteredNameIsValid의 초기값을 false로 설정하는 것이 좋다.
- enteredNameTouched state를 사용하여 submit버튼을 클릭 했을 때 유효성 에러를 출력하면 된다!!

### 2️⃣ 실시간 유효성 검사와 리팩토링

```js
const SimpleInput = (props) => {
  const [enteredName, setEnteredName] = useState("");
  // enteredNameIsValid state삭제
  const [enteredNameTouched, setEnteredNameTouched] = useState(false);

  // enteredName이 변경될 때마다 빈값인지 확인하여 상수로 사용
  const enteredNameIsValid = enteredName.trim() !== "";
  const nameInputIsInvalid = !enteredNameIsValid && enteredNameTouched;

  const nameInputChangeHandler = (event) => {
    setEnteredName(event.target.value);
  };

  const nameInputBlurHandler = (event) => {
    // input blur시 true로 변경
    // input은 이미 touch된 적이 있기 때문
    // 빈값일때 blur일 경우 error출력
    setEnteredNameTouched(true);
  };

  const formSubmissionHandler = (event) => {
    event.preventDefault();

    setEnteredNameTouched(true);

    // submit 실행 후 빈값일 경우 해당 로직 stop
    if (!enteredNameIsValid) {
      return;
    }

    console.log(enteredName);

    // nameInputRef.current.value = ''; => NOT IDEAL, DON'T MANIPULATE THE DOM
    setEnteredName("");
    // submit 실행 후 초기화
    setEnteredNameTouched(false);
  };

  return (
    <form onSubmit={formSubmissionHandler}>
      <div>
        <label htmlFor="name">Your Name</label>
        <input
          type="text"
          id="name"
          onChange={nameInputChangeHandler}
          onBlur={nameInputBlurHandler}
          value={enteredName}
        />
        {nameInputIsInvalid && <p>Name must not be empty.</p>}
      </div>
      <div>
        <button>Submit</button>
      </div>
    </form>
  );
};
```
