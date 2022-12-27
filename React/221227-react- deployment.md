# 리액트 앱 배포하기

1. Test Code
2. Optimize Code
3. Build App for Production
4. Upload Production Code to Server
5. Configure Server

## Optimize Code

- 큰 자바스크립트 코드 번들을 빌드하고, 해당 사이트를 사용하려면 웹사이트의 모든 방문자가 전체 번들을 다운받해야한다.
- 사용자가 웹사이트를 방문하게 되어 해당 사이트 화면에 보이는것과 사용하려면 리액트 코드를 다운받을때가지 기다려야 한다.
- 그러므로 초기 코드 번들을 가능한 작게 만들어야한다.

### ✔️ lazy loading

- `해당 코드가 필요할 때만 그 특정 코드를 로딩하는 것이다.`
- 코드를 여러 번들로 나누고, 각각 필요할 때만 다운로드하는 것이다.
- lazy loading을 사용하여 프로덕션용 앱을 빌드할 수 있다.

```js
const NewQuote = React.lazy(() => import("./components/pages/NewQuote"));
```

- 코드를 다운로드하는 동안 react는 중단되고, 다운로드가 완료될때까지 해당 컴포넌트를 로드할 수 없다. 그러므로 `대체 UI`를 정의해야한다.(일종의 대체 콘텐츠)

```js
import {Suspense} from 'react';
...
return (
  <Suspsense fallback={<p>loading...</p>}>
    <Routes>
    ...
    </Routes>
  </Suspsense>
)
```

## Build App for Production

- `npm run build`: 코드를 최대한 최적화하고 축소한다.
- build폴더: 최종적으로 배포하는 데 필요한 모든 코드가 포함되어 있다.

## Upload Production Code to Server

- build에 있는 js는 브라우저 사이드, 클라이언트 사이드 자바스크립트 코드이므로 build에 있는 파일들은 서버에서 실행되기 위한 코드들이 없음! 그러므로 `정적 사이드 호스트`가 필요하다!
- 그러므로 정적 웹사이트 호스트를 제공하는 호스트 제공자가 있어야한다.
