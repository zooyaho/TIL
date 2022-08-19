22.08.17 - TIL

# 🧐 자식컴포넌트에 함수프롭을 넘길때 인수 기본값을 설정하는 방법

## ● Function.prototype.bind()

: 함수를 호출하지 않고, 함수를 새롭게 생성해 반환한다.
: **매개변수를 고정하기 위해 사용한다.**

> - [Function.prototype.bind의 사용 예제](https://unordinarydays.tistory.com/158)

## ● 예제

: 버튼 클릭 시 text 변환

👾 app.js

```js
import Button from "./UI/Button";

const app = () => {
  const changeText = "Chage Text";
  const printHandler = (text) => {
    return text;
  };

  return (
    <>
      <Button onPrint={printHandler.bind(null, changeText)} />
    </>
  );
};
```

👉🏻 `printHandler.bind(null, changeText)` : 인수의 기본값을 설정하여 자식 컴포넌트에게 전달

👾 Button.js

```js
const Button = (props) => {
  const [printText, setPrintText] = React.useState("Default Text");
  const clickHandler = () => {
    // 기본값(Chage Text)으로 설정한 인수가 저장되어 출력됨!
    setPrintText(props.onPrint());
  };

  return (
    <>
      <button onClick={clickHandler}>Print Click!</button>
      <p>{printText}</p>
    </>
  );
};
```

👉🏻 `setPrintText(props.onPrint());` : 인수를 보내지 않아도 파라미터의 기본값이 설정되어 있기때문에 "Chage Text" 출력

🟢 클릭 전  
![](https://velog.velcdn.com/images/zooyaho/post/b2d47536-6bfc-4394-ad1e-3a305fadcd26/image.png)

🟢 클릭 시  
![](https://velog.velcdn.com/images/zooyaho/post/a8e11efb-a130-4daa-83f7-7f7f962817e3/image.png)
