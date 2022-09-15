# NextJS

- React기반의 풀스택 프레임워크
- 사전 렌더링 기능을 내장하기 때문에 NextJS는 싱글 페이지 애플리케이션을 제공하여 서버에 요청이 오면, 이 싱글 페이지에 동적으로 사전 렌더링을 거쳐 컨텐츠를 포함한 초기 페이지를 보여준다.

## ⭐️ 주요 기능 1: 내장 서버 측 렌더링(SEO향상)

- 서버 사이드 렌더링(SSR)을 내장하고 있다.
- SSR이란 페이지 콘텐츠를 클라이언트가 아닌 서버에서 준비하는 것
- 서버에서 React 페이지, React 컴포넌트를 사전에 렌더링하여 보여준다.
- 이러한 기능은 검색 엔진이 사용자가 보는 대로 보기 때문에,검색 엔진 최적화에 좋다.
- 또한 사용자에게 더 나은 초기 로딩 환경을 제공한다.(초기에 화면이 깜박이지 않음)
- 사용자에게 빠른 대화형 인터페이스를 제공한다.

## ⭐️ 주요 기능 2: 파일 기반 라우팅으로 라우팅 간소화

- 라우팅이란 기본적으로 URL을 감시하다가 URL이 바뀌면 백엔드 서버에 요청을 보내는 브라우저의 기본 동작을 막고 대신 React를 사용하여 페이지에 다른 콘텐츠를 렌더링하여 다른 컴포넌트를 보여주는 과정을 말한다.
- NextJS는 내장된 특수 페이지 폴더가 있으며, 파일과 폴더를 이용해 페이지와 라우트를 정의한다.
- NextJS는 중첩 라우팅이나 동적 매개변수를 이용한 동적 라우팅같이 우리에게 필요한 모든 기능을 지원한다.

## ⭐️ 주요 기능 3: 풀스택 앱 빌드

- NodeJS 코드를 이용해서 React 프로젝트에 백엔드 API를 쉽게 추가할 수 있다.
- 데이터베이스나 파일에 데이터를 저장하거나 거기에서 데이터를 받아오거나, 인증을 추가하는 등 모든 작업을 할 수 있다.
- 독립적으로 REST API 프로젝트를 구축하지 않고 하나의 프로젝트, 즉 Next 프로젝트에 클라이언트 쪽 코드와 React 사용자 인터페이스를 추가하고 백앤드 API 코드와 혼합하면 된다.

## 🔵 설치 & 사전 세팅

```js
npx create-next-app // NextJS app 설치
npm run dev // 개발 서버 실행
```

- pages/api 폴더 삭제
- pages/index.js 파일 삭제
- styles/Home.module.css 파일 삭제

> 공식 사이트 https://nextjs.org/

## 🔵 페이지 설정

- pages폴더에 생성한 파일 이름은 경로 이름으로 사용된다.
- 컴포넌트 이름은 마음대로 설정해도 됨.
- SSR이 기본적으로 작동한다.

### ● 중첩 경로 및 페이지 추가하기

- 폴더를 생성하고 중첩경로에 해당하는 파일을 생성한다. <br>
  ex) news 폴더 생성 &rarr; news/index.js(/news) &rarr; news/detail.js(/news/detail)

### ● 동적페이지 만들기

- 파일명을 `[식별자]`로 설정한다. <br>
  ex) [newsId].js

### ● 동적 매개변수 값 추출하기

- 함수형 컴포넌트에서 사용 가능

👾 pages/news/[newsId].js

```js
import { useRouter } from "next/router";

export default function DetailPage() {
  const router = useRouter();
  // 중첩객체에 접근 가능, [식별자] 값 추출
  const newsId = router.query.newsId;
  console.log(newsId);
  // newsId를 가지고 DB에서 해당 내용을 가져오면 된다.

  return <h1>The Detail Page</h1>;
}
```

### ● 페이지간 연결하기

- Link 컴포넌트 사용하기

```js
import Link from "next/link";
```

### ● \_app.js

- 최상위 컴포넌트
- 모든 페이지에서 필요한 처리를 수행한다.(모든 페이지에 공통적으로 적용된다)
- 페이지 간의 공통 레이아웃을 가질 수 있다.
- 공통 상태를 가질 수 있음(Store를 설정해두면 좋다)
- 글로벌 CSS (모든 페이지 공통)를 정의 할 수 있다.
- 각 Route 구성 요소를 래핑한다.
- Redux Provider 설정한다.

```js
import "../styles/globals.css";

function MyApp({ Component, pageProps }) {
  return (
    <Provider store={store}>
      <DefaultLayout>
        <Component {...pageProps} />
      </DefaultLayout>
    </Provider>
  );
}

export default MyApp;
```

## 🔵 페이지 렌더링 방법

```js
export default function HomePage() {
  const [loadedMeetups, setLoadedMeetups] = useState([]);

  useEffect(() => {
    // http request
    setLoadedMeetups(props.meetups);
  }, []);
  return <MeetupList meetups={loadedMeetups} />;
}
```

- index 렌더링, 빈배열 > useEffect실행, http호출 후 데이터 배열에 할당 > index 재 렌더링
- 해당 컴포넌트는 2번 렌더링이 됨
- 두번의 렌더링 때문에 검색 엔진 최적화에 문제가 생김
- nextJS는 두번째 렌더링을 기다리지 않음 > 첫번째 렌더링을 가지고 서버에서 미리 페이지를 생성함, 사전 렌더링한 HTML코드를 반환함
- HTML 페이지를 받은 후에 React가 받아가서 페이지에 Hydrate라고 부르는 작업을 수행함. 즉 React가 페이지를 싱글 페이지 어플리케이션으로 만들고 제어한다.
- useEffect작업은 서버가 아닌 브라우저에서 실행이 된다.
- 이를 위해 NextJS는 페이지 렌더링 형태의 사전 렌더링을 제공하며 두가지 방법이 있다. 서로 다른 시점에 코드가 실행 된다.

### ● 정적 사이트 생성(SSG - Static Site Generation)

- 사전 렌더링 되는 시점은 애플리케이션을 빌드하거나 Next 프로젝트를 빌드하는 시점(프로덕션용으로 빌드하는 시점)이다.
- 기본적으로 요청이 서버에 도달했을 때 서버에서 즉각적으로 페이지를 사전 렌더링 하지 않는다. 즉 사이트가 배포되고 나면 사전 렌더링한 페이지는 변경되지 않음.
- 페이지 컴포넌트에 데이터를 가져와 추가해야한다면 페이지 컴포넌트 파일에서만 적용되는 특수함수를 export하여 사용해야한다.

#### ⭐️ getStaticProps

- 컴포넌트 함수를 바로 호출하지 않고 반환된 jsx스냅샷을 HTML컨텐츠로 사용한다.
- props는 페이지에서 필요한 데이터를 포함할 수 있다.
- 비동기적으로 사용할 수 있어 유용하다.
- 이 함수는 promise를 반환하는데 NextJS가 promise가 해결될 때까지 기다린다. &rarr; 데이터를 읽어들일 때까지 기다린다.
- 그런다음 컴포넌트함수에서 사용하는 props를 반환한다.
- **getStaticProps안에서는 일반적으로 서버에서만 돌아가는 코드들을 실행할 수 있는데 파일 시스템에에 접근할 수 있고 데이터베이스에 연결할 수 있다.** &rarr; 이 코드들을 빌드 프로세스 중에 실행되기 때문에 클라이언트 측에서 절대 실행되지 않는다.
- 항상 객체를 반환해야 한다. (일반적으로 props 프로퍼티를 설정한다)
- 두번째 렌더링 사이클에서 데이터를 받는 것이 아니라 **초기에 페이지를 사전 렌더링 하기 전에 빌드 프로세스에서 받는다.**
- **사전 렌더링으로 데이터를 가져온다.**

```js
export default function HomePage(props) {
  /* const [loadedMeetups, setLoadedMeetups] = useState([]);

  useEffect(() => {
    // http request
    setLoadedMeetups(props.meetups);
  }, []); */
  return <MeetupList meetups={props.meetups} />;
}

export async function getStaticProps() {
  // fetch data from an API
  return {
    props: {
      meetups: DUMMY_MEETUPS,
    },
  };
}
```

#### 마주할 수 있는 문제

- 이 데이터에 최신 정보는 없을 수 있다는 문제가 있다. 항상 예전 정보만 가지고 있다.
- 개인 블로그와 같은 사이트를 데이터가 빈번하게 변하지 않아 유용하지만 데이터를 계속 업데이트하여 화면에 출력하는 사이트는 프러퍼티를 추가하여 관리해야한다.
- 데이터가 빈번하게 변하는 사이트는 데이터가 변할 때마다 사이트를 다시 빌드해서 다시 배포해야한다.
- 데이터가 빈번하게 변하는 사이트일 경우 revalidate 프러퍼티를 추가한다. revalidate는 점진적 정적 생성 기능을 수행한다.

##### revalidate 프러퍼티

- 숫자를 값으로 받는다.
- 숫자는 페이지를 다시 생성할 때까지 NextJS가 대기하는 시간을 초단위로 표시한다.
- 빌드 프로세스 중에 바로 생성되지 않고 대기 시간 이후로 페이지가 생성된다.
- 10이라면 10초마다 서버에서 페이지를 다시 생성한다.
- 다시 만들어진 페이지는 예전에 만들어 놓은 페이지를 대체한다.
- 일부 데이터가 변경되었다고 매번 다시 빌드하고 배포할 필요는 없다.

```js
export default function HomePage(props) {
  return <MeetupList meetups={props.meetups} />;
}

export async function getStaticProps() {
  // fetch data from an API
  return {
    props: {
      meetups: DUMMY_MEETUPS,
    },
    revalidate: 10, // 10초마다 페이지 재 생성
  };
}
```

✅ npm run build: 프러덕션 빌드 구축

### ● 서버 사이드 렌더링
