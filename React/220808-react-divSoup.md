22.08.08 - TIL

> [참고] Udemy - React 완벽 가이드 with Redux, Next.js, TypeScript

# jsx 제한 사항 및 해결 방법

- 루트 jsx 요소는 1개여야만 한다.
- jsx가 js로 변환하면서 값 하나만 반환 가능하기 때문이다.

```js
return (
	React.createElement('div', [ ], "Hi");
  );
```

- 그래서 인접한 요소들을 `<div>`로 감싸게 되는데, 불필요한 div요소가 중첩되어 `<div>` soup이 발생한다.

## 💡 1. 이상적인 해결방법

> wrapper 요소를 적용한다.

1️⃣ Helpers폴더를 만든다.  
2️⃣ Wrapper.js 컴포넌트를 만든다.

```js
// Wrapper.js
const Wrapper = (props) => {
  return props.children;
};
export default Wrapper;
```

3️⃣ Wrapper컴포넌트 import하기

```js
// App.js
import Wrapper from './Helpers/Wrapper'
...
return (
  <Wrapper>
    ...
  </Wrapper>
);
...
```

## 2. React에 내장된 컴포넌트 사용

### 1️⃣ <React.Fragment> 사용하기

⭐️

- `import React from 'react';`
- `<React.Fragment></React.Fragment>`

⭐️

- `import React, {Fragment} from 'react';`
- `<Fragment></Fragment>`

```js
// App.js
...
return (
  <React.Fragment>
    ...
  </React.Fragment>
);
...
```

### 2️⃣ <></> 사용하기

👉🏻 빌드 워크 플로우가 이를 지원할 경우 <></> 사용 가능

```js
// App.js
...
return (
  <>
    ...
  </>
);
...
```
