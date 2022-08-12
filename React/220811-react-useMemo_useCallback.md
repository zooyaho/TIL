22.08.11 - TIL

> [참고] Udemy - React 완벽 가이드 with Redux, Next.js, TypeScript

# ⚛︎ React.memo & useCallback & useMemo

## 1. React.memo()

- `React.memo(함수형 컴포넌트)` : 해당 컴포넌트에 어떤 props가 입력되는지 확인하고, 입력 되는 모든 props의 신규 값을 확인한 뒤 이를 기존의 props의 값과 비교하도록 리액트에게 전달한다. 그리고 props의 값이 바뀐 경우에만 컴포넌트를 재실행 및 재평가하게 된다.
- **부모 컴포넌트가 변경되었지만 자식 컴포넌트의 props값이 바뀌지 않았다면 컴포넌트 실행은 건너뛴다.**

### ● 예제

👾 App.js

```js
const App = () => {
  const [isShow, setIsShow] = useState(false);
  const clickHandler = () => {
    // toggle
    setIsShow((prev) => !prev);
  };

  return (
    <>
      // state인 isShow가 아닌 false로 고정됨.
      <DemoOutput show={false} />
      <Button onClick={clickHandler}>toggle</Button>
    </>
  );
};
```

👾 DemoOutput.js

```js
import MyParagraph from "./MyParagraph";

const DemoOutput = (props) => {
  return <MyParagraph>{porops.show ? "This is new!" : ""}</MyParagraph>;
};
export default React.memo(DemoOutput);
```

👉🏻 버튼 클릭 시 state가 변경되어 부모 컴포넌트(App)는 재평가가 일어나고 재평가가 일어나면서 jsx가 리턴하는데, 그 과정에서 자식 컴포넌트(DemoOutput)도 재평가가 일어나야하지만 `React.memo(DemoOutput)`로 인하여 DemoOutput의 props는 변경되지 않았기 때문에(현재 false로 고정되어 있음) DemoOutput컴포넌트는 재평가가 일어나지 않음!!  
🔥 불필요한 재렌더링을 피하여 최적화가 이루어지고 있음!!

### 🚨 props로 참조값을 넘길 경우

👾 Button.js

```js
const Button = (props) => {
  return <button onClick={props.onClick}>{props.children}</button>;
};
export default React.memo(Button);
```

👉🏻 부모컴포넌트는 함수이고, 재평가가 실행될 때마다 해당 함수는 재생성이 된다. props로 전달한 함수(onClick)의 값(함수의 주소를 가리키는 별칭)이 갱신되면서 React.memo()는 props가 변경된 것으로 판단하여 자식 컴포넌트(Button)를 재평가하게 된다!!! 이것은 리액트의 오류다!!  
👉🏻 함수의 재생성을 막기위해선 useCallback 훅을 사용하면 된다.

## 2. useCallback()

- **컴포넌트 실행 전반에 걸쳐 함수를 저장할 수 있게 하는 훅이다.**
- 해당 함수를 리액트의 내부 저장 공간에 저장해서 함수 객체가 실행될 때마다 재사용할 수 있게 한다.
- 저장한 함수는 매 실행때마다 재생성할 필요가 없다는걸 알린다.
- 동일한 함수 객체가 메모리의 동일한 위치에 저장되므로 이를 통해 비교 작업을 할 수 있다!
- `useCallback(매핑할 함수,[dependence])`
  - 첫 번째 인자: 매핑할 함수
  - 두 번째 인자: useCallback 호출에 대한 의존성 배열, useEffect와 같은 기능, **상태나 props, 컨텍스트를 지정할 수 있음.**
- `[dependence]` : 이 환경에서 사용할 수 있는 값에 클로저를 만들게 된다.

### ● 예제1

👾 App.js

```js
const App = () => {
  const [isShow, setIsShow] = useState(false);
  //useCallback 훅 사용
  const clickHandler = useCallback(() => {
    setIsShow((prev) => !prev);
  }, []);

  return (
    <>
      <DemoOutput show={false} />
      <Button onClick={clickHandler}>toggle</Button>
    </>
  );
};
```

👉🏻 위 예제에서 clickHandler를 useCallback훅 사용할 경우 버튼을 클릭해도 Button컴포넌트는 재평가가 되지 않는다!!(React.memo 사용)

### ● 예제2

: isAllow라는 토글버튼의 동작을 제어하는 state를 설정한 경우이다.

👾 App.js

```js
const App = () => {
  const [isShow, setIsShow] = useState(false);
  const [isAllow, setIsAllow] = useState(false);
  //useCallback 훅 사용
  const clickHandler = useCallback(() => {
    if (isAllow === true) {
      setIsShow((prev) => !prev);
    }
  }, [isAllow]);
  const allowClickHandler = () => {
    setIsAllow(true);
  };

  return (
    <>
      <DemoOutput show={false} />
      <Button onClick={clickHandler}>toggle</Button>
      <Button onClick={allowClickHandler}>allow button</Button>
    </>
  );
};
```

👉🏻 useCallback()의 의존성 배열을 빈값으로 둘 경우, useCallback으로 인하여 해당 함수(clickHandler)는 isAllow값이 변경되어도 재평가가 되지 않아 토글기능이 실행되지 않는다.  
👉🏻 의존성으로 isAllow을 추가해야 isAllow가 변경될 때 **함수안의 isAllow가 최신값으로 업데이트가 되어**, 해당 기능이 잘 실행된다!  
👉🏻 **이때 clickHandler는 클로저이고, isAllow는 자유변수이다!**

## 3. useMemo()

- 과거에 했던 모든 작업을 반복하고 싶지 않을 때 사용
- useCallback이 함수를 저장하는 것처럼 **모든 종류의 데이터를 저장할 수 있다.**
- 🚨 useMemo를 사용해 데이터를 저장하면 이는 메모리를 사용하는 것이고 useMemo 인수의 함수 저장 또한 일정 성능을 사용하는 것이므로 모든 값에 해당 훅을 사용해서는 안됨!
- 성능 집약적 작업등을 재수행 하지 않기 위해 useMemo훅을 사용함!
- `useMemo(함수, [dependence])`
  - 첫 번째 인자: 저장하고 싶은것을 반환하는 함수
  - 두 번째 인자: 의존성 배열

### 예제

: 버튼 클릭 시 제목이 변경되어 자식 컴포넌트인 DemoList도 재평가되어 **props로 받은 item들의 정렬 작업을 다시하게 된다.**  
: 특히 정렬은 컴포넌트에서 수행 가능한 대표적인 **성능 집약적 작업** 중 하나이다.  
: 이것을 해결하기 위해 useMemo훅을 사용하는 예제이다.

![](https://velog.velcdn.com/images/zooyaho/post/7b10d990-24d1-42a8-bdaa-e592d9d94969/image.png)

👾 App.js

```js
const App = () => {
  const [listTitle, setListTitle] = useState("My List");

  //  useCallback() 사용
  const changeTitleHandler = useCallback(() => {
    setListTitle("New Title");
  }, []);
  return (
    <div>
      <DemoList title={listTitle} items={[5, 3, 1, 10, 9]} />
      <Button onClick={changeTitleHandler}>Change List Title</Button>
    </div>
  );
};
```

👾 Button.js

```js
const Button = (props) => {
  return <button onClick={props.onClick}>{props.children}</button>;
};
// React.memo() 사용
export default React.memo(Button);
```

👾 DemoList.js

```js
const DemoList = (props) => {
  const sortedList = props.items.sort((a, b) => a - b);

  return (
    <div>
      <h2>{props.title}</h2>
      <ul>
        {sortedList.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
};
// React.memo() 사용
export default React.memo(DemoList);
```

#### 💡 useMemo적용

👾 App.js

```js
const App = () => {
  const [listTitle, setListTitle] = useState("My List");

  //  useCallback() 사용
  const changeTitleHandler = useCallback(() => {
    setListTitle("New Title");
  }, []);
  return (
    <div>
      <DemoList title={listTitle} items={useMemo(() => [5, 3, 1, 10, 9], [])} />
      <Button onClick={changeTitleHandler}>Change List Title</Button>
    </div>
  );
};
```

👉🏻 items는 **배열인 참조값이므로** App이 재평가 될때마다 메모리상 다른값이 된다.  
👉🏻 useMemo()를 사용하여 items를 리액트의 내부 저장 공간에 저장한다.  
👉🏻 이때 빈의존성 배열은 외부 의존성이 없기때문에, 값은 변함이 없는 하드코딩이 된다.

👾 DemoList.js

```js
const DemoList = (props) => {
  const { items } = props;
  const sortedList = useMemo(() => {
    return items.sort((a, b) => a - b);
  }, [items]);

  return (
    <div>
      <h2>{props.title}</h2>
      <ul>
        {sortedList.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
};
// React.memo() 사용
export default React.memo(DemoList);
```

👉🏻 item에 변경사항이 발생할 때만 리빌드를 진행하게 됨!!!  
👉🏻 버튼을 눌러도 처음 이후로 배열이 재정렬 되지 않음!
