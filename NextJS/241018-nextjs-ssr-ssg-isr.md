# Next.js에서 Page Router와 App Router를 활용한 SSR, SSG, ISR 구현 비교

> Next.js는 서버 사이드 렌더링(SSR), 정적 사이트 생성(SSG), 점진적 정적 재생성(ISR) 기능을 지원하여 웹 애플리케이션의 성능과 SEO를 최적화할 수 있습니다. 이번 포스트에서는 Page Router와 App Router를 사용하여 각 기능을 구현하는 방법을 비교해 보겠습니다.

## 1. SSR (Server-Side Rendering)

### Page Router에서의 SSR

SSR은 사용자가 페이지를 요청할 때마다 서버에서 HTML을 생성하여 클라이언트에 전달하는 방식입니다.

- **장점**: 항상 최신 데이터를 제공하며, SEO에 유리합니다.
- SSR을 구현하기 위해 페이지 컴포넌트 내에서 fetch를 사용하여 데이터를 요청하면 됩니다.
- 매 요청마다 서버에서 페이지가 생성되므로 항상 최신 데이터를 사용자에게 제공할 수 있습니다.
- 이 방식은 SEO에 유리하며, 데이터가 자주 변경되는 경우에 특히 유용합니다.

```javascript
// pages/posts/[id].js
export async function getServerSideProps(context) {
  const { id } = context.params; // URL에서 ID 추출
  const res = await fetch(`https://api.example.com/posts/${id}`);

  if (!res.ok) {
    return { notFound: true }; // 데이터가 없을 경우 404 페이지 반환
  }

  const post = await res.json();

  return {
    props: { post }, // 페이지 컴포넌트에 전달할 데이터
  };
}

const PostPage = ({ post }) => {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  );
};

export default PostPage;
```

### App Router에서의 SSR

App Router를 사용하면 페이지 컴포넌트 내에서 fetch를 사용하여 간단하게 SSR을 구현할 수 있습니다.

```javascript
// app/posts/[id]/page.js
const PostPage = async ({ params }) => {
  const res = await fetch(`https://api.example.com/posts/${params.id}`);

  if (!res.ok) {
    throw new Error("Failed to fetch data");
  }

  const post = await res.json();

  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  );
};

export default PostPage;
```

### 비교

- Page Router: getServerSideProps를 사용하여 요청 시마다 데이터를 가져오고, HTML을 생성합니다.
- App Router: 페이지 컴포넌트 내에서 fetch를 사용하여 동일한 기능을 수행합니다.

## 2. SSG (Static Site Generation)

### Page Router에서의 SSG

SSG는 페이지를 빌드 시 미리 생성하여 정적으로 제공하는 방식입니다.

- **장점**: 정적으로 제공되므로 빠른 페이지 로드 속도를 제공합니다. 그러나 데이터가 변경되려면 다시 빌드해야 합니다.
- SSG 예시는 동적 라우팅을 통해 API에서 가져온 데이터를 바탕으로 여러 페이지를 생성하는 데 사용됩니다.
- 동적 라우팅을 사용하면 각 URL에 대한 페이지를 쉽게 생성하고, SEO와 성능을 최적화할 수 있습니다.
- 일반적인 SSG 사용 예시가 동적 라우팅을 포함하는 것은 이러한 이점 때문입니다.

```javascript
// pages/posts/[id].js
export async function getStaticPaths() {
  const res = await fetch("https://api.example.com/posts");
  const posts = await res.json();

  const paths = posts.map((post) => ({
    params: { id: post.id.toString() }, // ID를 문자열로 변환
  }));

  return { paths, fallback: false }; // 미리 생성된 경로 외의 요청은 404 반환
}

export async function getStaticProps({ params }) {
  const res = await fetch(`https://api.example.com/posts/${params.id}`);

  if (!res.ok) {
    return { notFound: true }; // 데이터가 없을 경우 404 페이지 반환
  }

  const post = await res.json();

  return {
    props: { post }, // 페이지 컴포넌트에 전달할 데이터
  };
}

const PostPage = ({ post }) => {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  );
};

export default PostPage;
```

### App Router에서의 SSG

App Router를 사용하여 SSG를 구현하는 방법은 비슷하지만, generateStaticParams와 fetch를 사용하여 페이지를 설정합니다.

```javascript
// app/posts/[id]/page.js
export async function generateStaticParams() {
  const res = await fetch("https://api.example.com/posts");
  const posts = await res.json();

  return posts.map((post) => ({
    id: post.id.toString(), // ID를 문자열로 변환
  }));
}

const PostPage = async ({ params }) => {
  const res = await fetch(`https://api.example.com/posts/${params.id}`);

  if (!res.ok) {
    throw new Error("Failed to fetch data");
  }

  const post = await res.json();

  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  );
};

export default PostPage;
```

### 비교

- Page Router: getStaticPaths와 getStaticProps를 사용하여 페이지를 미리 생성하고 데이터를 가져옵니다.
- App Router: generateStaticParams를 사용하여 경로를 정의하고, fetch를 통해 데이터를 가져옵니다.

## 3. ISR (Incremental Static Regeneration)

## Page Router에서의 ISR

ISR은 SSG의 장점을 활용하면서 설정된 주기에 따라 페이지를 자동으로 재생성합니다.

- **장점**: 정적 페이지의 장점과 동적인 데이터 업데이트를 동시에 활용할 수 있습니다.
- 이를 통해, SSG의 장점(빠른 페이지 로드와 SEO 최적화)과 ISR의 장점(정기적인 데이터 업데이트)을 모두 활용할 수 있습니다.
- 페이지가 처음 요청될 때는 정적으로 생성되고, 이후에는 설정된 주기에 따라 새로운 데이터를 가져오도록 자동으로 업데이트됩니다.

```javascript
// pages/posts/[id].js
export async function getStaticPaths() {
  const res = await fetch("https://api.example.com/posts");
  const posts = await res.json();

  const paths = posts.map((post) => ({
    params: { id: post.id.toString() },
  }));

  return { paths, fallback: "blocking" }; // 페이지가 생성되지 않은 경우 서버에서 데이터 생성
}

export async function getStaticProps({ params }) {
  const res = await fetch(`https://api.example.com/posts/${params.id}`);

  if (!res.ok) {
    return { notFound: true };
  }

  const post = await res.json();

  return {
    props: { post },
    revalidate: 10, // 10초마다 재생성
  };
}

const PostPage = ({ post }) => {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  );
};

export default PostPage;
```

### App Router에서의 ISR

App Router에서 ISR을 적용하려면 fetch 요청에 next: { revalidate: seconds }를 추가합니다.

```javascript
// app/posts/[id]/page.js
export async function generateStaticParams() {
  const res = await fetch("https://api.example.com/posts");
  const posts = await res.json();

  return posts.map((post) => ({
    id: post.id.toString(),
  }));
}

const PostPage = async ({ params }) => {
  const res = await fetch(`https://api.example.com/posts/${params.id}`, {
    next: { revalidate: 10 }, // 10초마다 재생성
  });

  if (!res.ok) {
    throw new Error("Failed to fetch data");
  }

  const post = await res.json();

  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  );
};

export default PostPage;
```

### 비교

- Page Router: getStaticProps에서 revalidate 속성을 사용하여 재생성을 설정합니다.
- App Router: fetch 요청 시 next: { revalidate: seconds } 옵션을 사용하여 재생성을 설정합니다.
