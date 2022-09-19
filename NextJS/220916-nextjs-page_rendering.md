# 🔵 페이지 렌더링 방법

✅ 기본 React 방식

- index 렌더링, 빈배열 > useEffect실행, http호출 후 데이터 배열에 할당 > index 재 렌더링
- 해당 컴포넌트는 2번 렌더링이 됨
- 두번의 렌더링 때문에 검색 엔진 최적화에 문제가 생김
- useEffect작업은 서버가 아닌 브라우저에서 실행이 된다.

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

✅ NextJS 방식

- nextJS는 두번째 렌더링을 기다리지 않음 > 첫번째 렌더링을 가지고 서버에서 미리 페이지를 생성함, 사전 렌더링한 HTML코드를 반환함
- HTML 페이지를 받은 후에 React가 받아가서 페이지에 Hydrate라고 부르는 작업을 수행함. 즉 React가 페이지를 싱글 페이지 어플리케이션(SPA)으로 만들고 제어한다.
- 이를 위해 NextJS는 페이지 렌더링 형태의 사전 렌더링을 제공하며 두가지 방법이 있다. 서로 다른 시점에 코드가 실행 된다.
- NextJS는 사전 렌더링을 페이지 처음에만 하고, 추후에는 SPA(기본 React)으로 돌아간다.

## ● 정적 사이트 생성(SSG - Static Site Generation)

📎 `사전 생성`: 콘텐츠를 구성하는 모든 HTML 코드와 모든 데이터를 사전에 준비시켜 놓는다는 뜻<br>

- 사전 렌더링 되는 시점은 애플리케이션을 빌드하거나 Next 프로젝트를 빌드하는 시점(프로덕션용으로 빌드하는 시점 - 배포 전 애플리케이션을 구축할 때)이다.
- 배포되고 나면 **구측된 페이지는 서버나 앱을 실행시키는 CDN을 통해서 캐시로 저장된다.** 이에 사전 구축된 페이지를 통해서 **즉시 입력 요청이 실행**될 수 있다.
- 모든 페이지가 한번씩 사전 렌더링이되고 추후에는 일반적인 React앱으로 돌아간다.
- NextJS에 이 페이지가 여전히 사전 생성되어야 하도록 한다는 것을 알릴수 있다.
- NextJS에 어떤 페이지를 사전에 생성해야 하는지 지정해야하는데 사전 생성할 페이지에 어떤 데이터를 포함해야하는 지를 **페이지 컴포넌트 파일에서만 적용되는 특수함수를 export하여 사용해야한다.**

### ⭐️ getStaticProps

```js
export async function getStaticProps(props){...}
```

- 일반적인 클라이언트 사이드 코드에는 컴포넌트 코드가 포함되고, getStaticProps함수 코드와 파일시스템 코드등은 포함되지 않는다.
- getStaticProps안에서는 **일반적으로 서버에서만 돌아가는 코드들을 실행할 수 있는데** 파일 시스템에에 접근할 수 있고 데이터베이스에 연결할 수 있다.
- 해당 함수내에 작성한 코드는 클라이언트가 볼 수 없다.
- **getStaticProps는 promise를 반환하는 비동기 함수로 그 안에 await 키워드를 사용할 수 있으며 NextJS가 promise인 데이터를 읽어들일 때까지 기다린다.**
- 항상 객체를 반환해야 한다. (필수로 props 프로퍼티를 설정한다)
- next build(npm run build) 즉 build 스크립트로 프로젝트를 빌드할 때 실행된다.

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

👾 파일시스템을 이용한 데이터 작업

```js
import path from "path"; // 이 모듈은 경로를 구축하는데 유용한 기능들이 있다.
/*
📎 파일 시스템
- import fs from 'fs'; // Node.js로부터 파일시스템 모듈을 임포트한다.
- 이 패키지는 설치가 필요한 서드파티 패키지가 아닌 Node.js의 핵심 모듈이다.
- 브라우저 측 javascript가 파일시스템에 접근할 수 없기때문에 클라이언트 사이드(페이지 컴포넌트)에서는 fs 모듈 작업이 안된다.
*/
import fs from "fs/promises"; // Node.js로부터 파일시스템 모듈을 임포트한다. promise를 반환한다.

// 2️⃣ 페이지 컴포넌트 함수를 두번째에 실행한다.
export default function HomePage(props) {
  const { products } = props;

  return (
    <ul>
      {products.map((product) => (
        <li key={propduct.id}>{propduct.title}</li>
      ))}
    </ul>
  );
}

// 1️⃣ getStaticProps가 있다면 이 함수를 첫번째 실행하고
export async function getStaticProps(context) {
  // 여기에 작성하는 코드는 클라이언트 사이드에 절대 보이지 않는 코드로 데이터를 페칭한다.

  /*
  📎 fs
  - readFileSync(): 파일을 동기적으로 읽고 완료될때까지 실행을 차단한다.  
  - readFile(path): 계속 실행하려면 콜백을 해야한다.
  📎 path
  : path.join에 어디서 시작하는지 알려주고 다른 Node.js객체로 현재 작업 디렉토리를 이동할 수 있다.
  - join(디렉토리, data가 있는 폴더명, 사용하려는 파일 이름)
  - process 객체: Node.js에서 전역적으로 사용할 수 있는 객체이다.
  - cwd(): 현재 작업 디렉토리를 뜻함.
  */

  // NectJS는 파일이 루트 프로젝트 폴더에 있는 것처럼 취급한다.
  // 현재 작업 디렉토리가 pages폴더가 아닌 모든 전체 프로젝트 폴더가 된다.
  // process.cwd()는 이 전체 프로젝트 디렉토리에서 시작한다고 알려준다.
  const filePath = path.join(process.cwd(), "data", "dummy_backend.json"); // 절대 경로 구축
  const jsonData = await fs.readFile(filePath);
  const data = JSON.parse(jsonData); // 일반적인 js객체로 변환

  return {
    props: {
      // 페이지 컴포넌트의 props로 해당 데이터를 전달한다.
      products: data.products,
    },
  };
}
```

#### ✔️ 마주할 수 있는 문제

- 페이지를 사전 생성하는 것은 정적인 데이터를 구축하는 경우에 좋다.
- 이 데이터에 최신 정보는 없을 수 있다는 문제가 있다. 항상 예전 정보만 가지고 있다.
- 개인 블로그와 같은 사이트를 데이터가 빈번하게 변하지 않아 유용하지만 **데이터를 계속 업데이트하여 화면에 출력하는 사이트는 프러퍼티를 추가하여 관리**해야한다.
- 데이터가 빈번하게 변하는 사이트는 데이터가 변할 때마다 사이트를 다시 빌드해서 다시 배포해야한다.
- 데이터가 빈번하게 변하는 사이트일 경우 `revalidate 프러퍼티`를 추가한다. revalidate는 점진적 정적 생성 기능을 수행한다.
- 캐시를 이용할 수 있다.

#### 🔔 증분 정적 생성(ISR)

- 페이지를 빌드할 때 정적으로 한번만 생성하는 것이 아닌 배포 후에도 재배포 없이 계속 업데이트 된다는 뜻
- 지정한 시간이 지나면 서버에서 재생성된 가장 최신의 페이지를 제공한다.(오래된 기존 페이지를 대체하고 캐시한다)
- 사전 렌더링을 계속 수행할 수 있다.

#### ✨ revalidate 프러퍼티

- 페이지를 다시 생성할 때까지 NextJS가 대기하는 시간을 초단위로 지정한다.
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

#### ✨ 데이터 fetching 실패 시 수행가능한 속성

```js
export async function getStaticProps(context) {
  ...

  if(!data){
    // 아예 DB에 엑세스할 수 없을 경우
    return {
      redirect: {
        destination: 'no-data',
      }
    }
  }

  if (data.products.length === 0) {
    return { notFound: true };
  }

  return {
    props: {...},
    revalidate: 10,
    // notFound: true
  }
}
```

- `notFound : true | false`
  - true 설정 : 페이지가 404 오류를 반환하며, 일반 페이지 대신에 404오류 페이지를 렌더링한다.
  - 데이터 fetching 실패 시 수행한다.
- `redirect : { destination: 'path'}`
  - 사용자를 리디렉션 시킨다.
  - 데이터 fetching 실패 시 수행한다.

### 동적 매개변수 작업

```js
import fs from "fs";
import path from "path";

export default function ProductDetailPage(props) {
  const { loadedProduct } = props;
  return (
    <>
      <h1>{loadedProduct.title}</h1>
      <p>{loadedProduct.description}</p>
    </>
  );
}

export async function getStaticProps(context) {
  const { params } = context;
  const productId = params.pid;

  const filePath = path.join(process.cwd(), "data", "dummy_backend.json"); // 현재 작업 디렉토리 / 폴더 / 데이터파일
  const jsonData = await fs.readFile(filePath);
  const data = JSON.parse(jsonData);

  const product = data.products.find((product) => product.id === productId);

  console.log(product);

  return {
    props: {
      loadedProduct: product, // product
    },
  };
}
```

- 매개변수 `context` : 경로상의 동적 세그먼트에 대한 구체적인 값을 알수 있다.
- `params`: 키-값 쌍의 객체
  - 서버상에서 작업하기 위한 매개변수를 받을 수 있다.
  - 데이터 사전 준비를 위한 매개변수를 받는다.
  - useRouter로 가져온 매개변수는 브라우저상에서 작업할 때 사용하는 것이다.

🚨 error 발생<br>

- NextJS가 모든 페이지를 사전 렌더링 하는데 **동적 페이지에서는 사전 렌더링 하지 않는다.**
- 여기서 동적 페이지는 [pid].js와 같은 동적 세그먼트가 있는 페이지를 말한다.
- NextJS는 동적 페이지를 위해 얼마나 많은 페이지를 미리 생성해야 하는지 알지 못하기 때문에 사전 렌더링이 되지않는다.
- 대신 서버에서 그때그때 생성된다!!

![](https://velog.velcdn.com/images/zooyaho/post/c5dc48aa-b2bb-4d01-958a-b5749de40399/image.png)

🔔 동적 페이지에서 NextJS는 어떤 [id]값이 사용 가능한지 어떤 동적 세그먼트 값을 사용할 수 있는지 알아야한다.

### ⭐️ getStaticPaths

```js
export async function getStaticPaths() {
  // NextJS가 각 ID에 대해 getStaticProps()를 세번 호출한다.
  return {
    paths: [
      { params: { pid: "p1" } },
      { params: { pid: "p2" } },
      { params: { pid: "p3" } },
    ],
    fallback: false,
  };
}
```

- 동적 페이지의 어떤 인스턴스를 생성할지 NextJS에 알린다.
- 객체를 반환.

#### 🔸 paths

- `paths`가 키고 인스턴스의 path의 데이터 객체를 가지는 배열값을 갖는다. (필수)
- `params: { [id] : 'id값' }`
  - key: 이 페이지로 연결하는 각각의 동적 세그먼트 식별자

#### 🔸 fallback

- `fallback: true | false`
- 블로그의 수백 글이 있고, 방문객수는 없을 때 미리 모든 게시글을 사전 생성은 시간과 자원낭비이다.
- `fallback: true`는 일부 페이지만 사전 렌더링할 수 있다.
- `fallback: true`는 **파일에서 찾을 수 없는 ID에 대해서도 페이지를 렌더링할 수 있다. 동적 세그먼트의 모든 값에 대한 페이지를 일일이 다 사전 생성할 필요가 없다!**

👾 p1페이지만 사전 렌더링하는 예시

```js
export async function getStaticPaths() {
  return {
    paths: [{ params: { pid: "p1" } }],
    fallback: true,
  };
}
```

🚨 문제

- url로 직접 페이지에 접속하게되면(페이지에 새로운 요청을 보내면) 에러가 발생한다.
- 에러의 이유는 동적 사전 생성 기능이 즉시 끝나지 않기 때문이다.
- 그러므로 **fallback을 사용하려면 해당 페이지 컴포넌트에서 폴백 상태를 반환할 수 있게 해야한다.**

✅ 해결

```js
export default function ProductDetailPage(props) {
  const { loadedProduct } = props;

  if (!loadedProduct) {
    // 사전 생성할 부분이 존재하는지 확인하고
    // 존재하지 않는다면 Loading과 같은 내용(폴백 컨텐츠)을 반환한다.
    return <p>Loadig...</p>;
  }

  return (
    <>
      <h1>{loadedProduct.title}</h1>
      <p>{loadedProduct.description}</p>
    </>
  );
}
...
export async function getStaticProps(context) {
  const { params } = context;
  const productId = params.pid;

  const data = await getData();
  const product = data.products.find((product) => product.id === productId);

  return {
    props: {
      loadedProduct: product, // product
    },
  };
}
```

### 존재하지 않은 페이지를 요청할 경우

- `{ notFound: true }`: 반환한다.
- 데이터가 누락된 일반 페이지를 반환하는 대신에 페이지를 찾을 수 없다는 404에러 페이지를 띄움.

```js
export async function getStaticProps(context) {
  const { params } = context;
  const productId = params.pid;

  const data = await getData();
  const product = data.products.find((product) => product.id === productId);

  if (!product) {
    // id에 대한 페이지가 없을 경우
    return { notFound: true };
  }

  return {
    props: {
      loadedProduct: product, // product
    },
  };
}
```

## ● 서버 사이드 렌더링(SSR)

- getStaticProps와 getStaticPaths는 일반적으로 프로젝트를 구축할 때 호출하기 때문에 내부에서 들어오는 실제 유입되는 실제 요청에 접근할 방법이 없다.

- SSR은 유입되는 모든 요청에 대한 페이지를 사전 렌더링한다. 그래서 유입되는 모든 요청에 대해서나 서버에 도달하는 특정 요청 객체에 접근할 필요가 있다. 예를들어 쿠키를 추출하는 경우이다.
- 페이지 컴포넌트 파일을 추가할 수 있는 함수를 제공하는데, 이 함수는 페이지 요청이 서버에 도달할 때마다 실행되는 함수이다. 서버에서만 작동하는 코드이다.
- 애플리케이션을 배포한 후 유입되는 모든 요청에 대해서만 재실행된다.

- 빌드 프로세스 중에는 실행되지 않고 요청이 들어왔을때만 **서버에서 실행이 된다.**
- 요청이 들어올 때까지 페이지가 만들어지는 것을 기다려야 한다.
- 매초 여러 번 바뀌는 데이터를 가지고 있을 때 사용하는 것이 좋다.

### ⭐️ getServerSideProps

```js
export async function getServerSideProps(context) {
  return {
    props: {},
    // notFound: true | false (op)
    // redirect: { destination: 'path' } (op)
  };
}
```

- 정의한 getServerSideProps 함수마다 들어오는 요청에 전부 유효성 검사를 실행하기 때문에 revalidate속성은 필요하지 않다~!
- 페이지 컴포넌트 파일에서만 사용할 수 있으며, 이 함수가 있다면 해당 페이지에 대한 요청이 들어올 때마다 실행된다.
- 인자로 context와 getStaticProps를 받을 수 있다.
- 요청 객체에 접속할 필요가 없다면(인증처럼) getStaticProps이 좀 더 낫다.
- 서버 사이드 코드에서 모든 요청을 처리하기 때문에 사전 생성할 필요도 없고 따라서 동적 경로 또한 미리 설정할 필요가 없다.

🔔 예를들어 사용자 프로필 페이지를 구현할 때, [uid].js와 같은 동적 페이지는 다른 사용자가 url을 통해 볼 수 있게되므로, **쿠키와 헤더가 든 요청 객체에 접근해서 어느 사용자가 요청을 보냈는지 알아낸다.**

#### ⭐️ context

```js
export async function getServerSideProps(context) {
  const { params, req, res } = context; // 동적 컴포넌트라면 접근 가능!

  console.log(req); // 기본 입력 메세지
  console.log(res); // 응답에 대한 객체

  return {...}
}
```

- 요청(req), 응답(res) 객체 전체에 접근 가능
- 요청(req)은 Next.js가 자동으로 해주며, 요청이 가기전에 헤더를 추가하거나 쿠키 추가 등 조정할 수 있다. 또한 서버에 도달한 요청 객체를 분석해서 거기서 들어오는 데이터(헤더나 쿠키 데이터등)를 읽을 수도 있다.
- `const req = context.req;`: 요청 객체, 들어오는 요청에 접근, header와 body에도 접근과 수정 가능, 추가 데이터 정보를 받을 수 있다.
- `const res = context.res;`: 응답 객체, 응답을 리턴하지 않고 prop key로 객체를 리턴한다. 이 키가 페이지 컴포넌트 함수 prop을 저장하고 있다.

```js
export default function HomePage(props) {
  return <MeetupList meetups={props.meetups} />;
}

export async function getServerSideProps(context) {
  const req = context.req; // 요청 객체
  const res = context.res; // 응답 객체

  // fetch data from an API
  return {
    props: {
      meetups: DUMMY_MEETUPS,
    },
  };
}
```

## λ

- npm run build
  ![](https://velog.velcdn.com/images/zooyaho/post/aebb6d1b-fbcd-42ed-b075-430c85bfd698/image.png)

👉🏻 `λ`: 람다 기호가 있는 페이지들은 사전 생성하지 않고 서버 측에서만 사전 렌더링됐다는 뜻이다.

## 클라이언트 사이드 데이터 페칭(Client-side data fetching)

### 페이지를 사전 렌더링할 필요가 없는 경우

- 데이터가 매초마다 여러 차례 변경된다면 프리페칭과 사전 렌더링을 하는 의미가 없음
- 특정 유저에만 한정되는 데이터( 온라인 쇼핑몰의 최근 주문 내역과 같은 데이터 )
- 계정에 접속해서 프로필 페이지에서 특정한 데이터를 열람하는 경우 &rarr; 검색 엔진에서도 개인 프로필을 확인하지 않음<br>
  👉🏻 접속하자마자 처음부터 데이터가 있는 것보다는 페이지상에서 탐색이 빠른게 더 중요하다.
- 다양한 데이터가 표시되는 대시보드 페이지의 경우, 모든 데이터를 한 번에 불러오도록 하면 서버에서 대시보드 요청을 처리하는 데 시간이 많이 소요되므로 개발 단계에서 이 페이지를 사전 렌더링할 이유가 없고 React 애플래케이션에 포함된 데이터를 사용자가 페이지에 방문할 때만 불러오도록 해야한다.

### 페이지 사전 렌더링(Pre-rendering)

### 데이터 프리페칭(Pre-fetching)
