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
