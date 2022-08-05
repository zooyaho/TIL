220805 - TIL

> [참고] Udemy - React 완벽 가이드 with Redux, Next.js, TypeScript

# 상태 끌어올리기

## ● 자식에서 부모 컴포넌트로 데이터 보내기!!

- props를 활용하여 부모 컴포넌트로 데이터 보내기

👾 App.js

```js
const App = () => {
  const [inputValue, setInputValue] = React.useState("");
  const addInputValueHandler = (inputValue) => {
    setInputValue(inputValue);
  };
  return (
    <>
      // props으로 pointer 전달
      <Child onAddInputValue={addInputValueHandler} />
      사용자 입력 값 : {inputValue}
    </>
  );
};
```

👾 Child.js

```js
const Child = (props) => {
  const [newValue, setNewValue] = React.useState("");
  // newValue 전달해서 저장
  props.onAddInputValue(newValue);
  return (
    <input
      value={newValue} // ⭐️ 양방향 바운딩
      onChange={(e) => {
        setNewValue(e.target.value);
      }}
    />
  );
};
```

🔦 출력
![](https://velog.velcdn.com/images/zooyaho/post/238f84b5-cdbf-4d43-9ee9-6c9cf0563cff/image.png)
