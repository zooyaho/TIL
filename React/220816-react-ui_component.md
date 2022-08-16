22.08.16 - TIL

> [참고] Udemy - React 완벽 가이드 with Redux, Next.js, TypeScript

# 💡 UI 컴포넌트 재사용

> **재사용 가능한 사전 스타일링된 유틸리티 컴포넌트를 만들어 사용한다.**

- 예를들어 input요소는 큰 프로젝트에서 빈번하게 사용할 것이다. 그러므로 input요소의 재사용 방법을 알아보자.
- Layout폴더를 생성하여 재사용할 요소의 컴포넌트파일을 생성한다.

👾 Input.js

```js
const Input = (props) => {
  return (
    <div>
      <label htmlFor={props.input.id}>{props.label}</label>
      <input {...props.input} />
    </div>
  );
};
export default Input;
```

👉🏻 **{...props.input} 전개 연산자를 사용할 경우, props.input에서 받아온 모든 키-값 쌍은 프롭으로 input에 추가된다!**

👾 Form.js

```js
const Form = (props) => {
  return (
    <form>
      <Input
        label="Age"
        input={{
          // 각각 input에 대한 내장 프롭들이다!
          id: "age" + props.id,
          type: "number",
          min: "1",
          max: "5",
          step: "1",
          dafaultValue: "1",
        }}
      />
    </form>
  );
};
export default Form;
```

👉🏻 input프롭으로 객체에 기본프롭들을 담아 전달한다. 인풋에 배포하면 자동으로 작동됨!!
