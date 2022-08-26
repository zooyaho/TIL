### ● Prompt컴포넌트를 활용한 경고창 표시하기

#### ✔️ Prompt

: 이 컴포넌트는 우리가 다른 곳으로 이동할 때 자동으로 감지함. 그리고 특정 조건이 충족되면 떠나기 전 경고를 표시해 줌.

- `when`: 사용자가 url을 변경하는 경우 이 프롬프트가 표시되어야 하는지 여부를 확인하는 프롭, true/false를 저장
- `message`: 보여주고 싶은 텍스트가 포함된 문자열을 반환하는 함수를 지정
  - `(location)=>'메세지'` : 페이지에 대한 정보를 담고 있는 어떤 위치 객체를 인수로 받음. 보여줄 메세지를 반환해야함.

```js
import { Prompt } from "react-router-dom";

<Prompt when={true} message={(location) => "보여줄 메세지를 반환!"} />;
```

#### 💡 예제

: 예를들어 폼양식 작성 후 사용자가 실수로 뒤로가기 버튼을 눌러, 작성돼있는 글이 reset되지 않게 propmt창을 사용하여 사용자에게 경고창을 표시하는 것이 좋다.

1️⃣ 양식이 포커스가 돼있는지 확인한다. form에 onFocus이벤트를 등록하여 핸들러 함수에 이 양식이 포커스 되었다는 정보를 저장한다.

```js
const [isEntered, setIsEntered] = useState(false);
const formFocusedHandelr = ()=>{
  // 사용자가 양식에서 작업하고 있음을 알 수 있음.
  setIsEntered(true);
}
...
<form onFocus={formFocusedHandelr}>
...
```

2️⃣ Prompt컴포넌트 사용하여 isEntered가 true일때 화면을 이동할 경우 경고창 띄움.

```js
import { Prompt } from 'react-router-dom'
...
<Prompt when={isEntered} message={(location)=> '정말로 이 화면을 떠나겠습니까?, 입력한 양식이 사라집니다.'}/>
<form onFocus={formFocusedHandler}>
...
```

3️⃣ 양식 작성 후 폼전송 버튼 클릭 시 프롬프트창 뜨지 않게 하기 위해 폼전송 버튼에 양식작성을 마쳤다는 함수를 등록한다.

💡 submit핸들러 함수에서 `setIsEntered(false);`로 설정하면 안됨.
setIsEntered 이 상태 업데이트를 실제로 탐색 작업을 트리거하기 전에는 진행되지 않음.
이렇게 따로 함수를 생성하여 변경해야 실제 양식 제출 처리 전에 트리거가 된다!

```js
...
const finishEnteringHandler = ()=>{
  setIsEntered(false);
}
...
<button onclick={finishEnteringHandler}>Add Todo</button>
```
