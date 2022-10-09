**목차**
- fetching data
- loading/ error states
- dev tools
- pagination
- prefetching
- mutations

## 🔗 Get start
### 1. 설치 
> npm i react-query@^3

### 2. query client
- 쿼리와 서버의 데이터 캐시를 관리하는 클라이언트 생성
### 3. queryProvider
- 자녀 컴포넌트에 캐시와 클라이언트 구성을 제공할 queryProvider 적용
```js
improt { QueryClientProvider, QueryClient } from 'react-query';
```
### 4. ReactQueryDevtools 
```js
improt { ReactQueryDevtools } from 'react-query/devtools';
```
### 5. useQuery 
- 서버에서 데이터를 가져옴
- 기본값으로 3회 재시도하여 데이터를 요청한다.

#### ⭐️ isLoading vs isFetching
- isFetching: **비동기 쿼리가 해결되지 않았음을 의미**(페칭을 완료하지 않았다는 의미)
- isLoading: isFetching의 하위 집합, 가져오는 상태에 의미가 있음. **비동기 쿼리가 해결되지 않고 캐시된 데이터 또한 없는 것**. 참이면 isFetching 또한 참이다!

### 📎 stale time vs cache time
- stale data: 만료된 데이터,
- **데이터 리페칭은 만료된 데이터에서만 발생할 수 있다.**
- `stale time`: 데이터를 허용하는 최대 나이, 기본값 0
- cache는 나중에 사용할 수 있는 데이터
- 특정 쿼리에 대한 활성 useQuery가 없는 경우 해당 데이터는 cold storage로 이동, 구성된 cache time이 지나면 캐시의 데이터가 만료되며 **유효시간의 기본값은 5분**(cache time의 기본값)이다.
- `cache time`은 특정 쿼리에 대한 useQuery가 활성화된 후 경과한 시간, 페이지에 표시되는 컴포넌트가 특정 쿼리에 대해 useQuery를 사용한 시간, **데이터가 비활성화 된 이후 남아 있는 시간**

## 🔗 pagination , prefetching, mutaion
- 게시글 클릭 시 댓글이 fetching이 일어나지 않음 
👉🏻 해결) 새 블로그 게시글 제목을 클릭할 때마다 데이터를 무효화 시켜 데이터를 다시 가져오게 한다.
👉🏻 문제) 위의 해결 방법은 게시글2를 클릭하면 게시글1의 comments data가 캐시에서 삭제가 되기때문에 다른 방법을 사용해야한다(같은 캐시공간을 사용해야한다)
👉🏻 찐 해결 방법) 각 게시물에 대한 쿼리에 라벨을 설정하면 된다. -> **쿼리키에 문자열 대신 배열을 전달** ex) ['comments', postId]

### ● pagination
- 현재 페이지를 파악해줌.
- 페이지마다 쿼리키가 필요함으로 쿼리키를 배열로 업데이트해서 fetching한다.
- 아래 코드는 next페이비 버튼 클릭 시 로딩 인디케이션이 발생하여 사용자 경험을 방해함 -> 데이터를 가져올 동안 사용자가 기다리지 않게 prefetching을 사용한다!!

```js
const [currentPage, setCurrentPage] = useState(1); // 현재 페이지

// data에 다음 페이지 유무에 대한 프로퍼티를 가지고 있다면 버튼을 비.활성화할지 판단하는 지표로 사용한다
const { data, isLoading, isError, error } = useQuery(
    ["posts", currentPage],
    () => fetchPosts(currentPage),
    {
      staleTime: 2000,
      keepPreviousData: true, // 이전 페이지로 돌아갔을 때 캐시에 해당 데이터가 있도록 해준다
    }
  );

...


return (
  ...
   <div className="pages">
     <button
       disabled={currentPage <= 1}
       onClick={() => {
         setCurrentPage((prevValue) => prevValue - 1);
       }}
   	 >
      Previous page
     </button>
     <span>Page {currentPage}</span>
     <button
        disabled={currentPage >= maxPostPage}
        onClick={() => {
          setCurrentPage((prevValue) => prevValue + 1);
        }}
     >
      Next page
     </button>
   </div>
);
```

### ● prefetching
- 데이터를 미리 가져와 캐시에 넣어 사용자가 기다릴 필요 없게 한다.
- 데이터의 기본값은 `stale(만료)`상태이다!!
- 데이터를 사용할 때 만료상태에서 캐시 데이터를 다시 가져오는 로직임.
- 예를 들어 `탭과 같이 통계적으로 특정 탭을 누를 확률이 높을 경우` 해당 데이터를 미리 가져오는 것이 좋음!!

```js
import { useQuery, useQueryClient } from "react-query";

const maxPostPage = 10;

const queryClient = useQueryClient();
  useEffect(() => {
    if (currentPage < maxPostPage) {
      // 페이지가 11일 때 fetch하지 않게
      const nextPage = currentPage + 1;
      queryClient.prefetchQuery(["posts", currentPage], () =>
        fetchPosts(nextPage)
      );
    }
  }, [currentPage, queryClient]);
```
📌 `prefetchQuery(["posts", currentPage], ...)`
💥 currentPage -> 의존성 배열에 추가하는 이유
- 쿼리 데이터는 만료(stale) 상태이지만 리페치(refetch)를 트리거할 대상이 없기 때문이다!
💥 "posts"
- 데이터 무효화를 시작할때 중요한 부분이며, 데이터를 무효화 하려면 모든 배열에 공통점이 있어야 한다.
- 특히 공통 접두사가 있으면 한번에 모두 무효화 시킬 수 있다!!

✅ useQuery(, , {keepPreviousData: true})
`keepPreviousData: true,` // 이전 페이지로 돌아갔을 때 캐시에 해당 데이터가 있도록 해준다. 쿼리 키가 변경될 때까지 이전의 모든 데이터가 그대로 유지된다. 페치하는 동안 이전 데이터를 자리표시자로 사용하는 것

⭐️ isLoading vs isFetching
```js
export function Posts() {
  const [currentPage, setCurrentPage] = useState(1); // 현재 페이지
  const [selectedPost, setSelectedPost] = useState(null);

  const queryClient = useQueryClient();
  useEffect(() => {
    if (currentPage < maxPostPage) {
      // 페이지가 11일 때 fetch하지 않게
      const nextPage = currentPage + 1;
      queryClient.prefetchQuery(["posts", currentPage], () =>
        fetchPosts(nextPage)
      );
    }
  }, [currentPage, queryClient]);

  // data에 다음 페이지 유무에 대한 프로퍼티를 가지고 있다면 버튼을 비.활성화할지 판단하는 지표로 사용한다
  const { data, isLoading, isError, error } = useQuery(
    ["posts", currentPage],
    () => fetchPosts(currentPage),
    {
      staleTime: 2000,
      keepPreviousData: true, // 이전 페이지로 돌아갔을 때 캐시에 해당 데이터가 있도록 해준다
    }
  ); // key / 비동기 함수
  // fetchPosts가 비동기이기 때문에 완료되기 전까지는 query에 저장되지 않음
  if (isLoading) return <div>Loading....</div>;
  if (isfetching) return <div>Fetching in pregress</div>;
  if (isError) return <div>{error.toString()}</div>;

  return (...);
}
```
- next페이지 버튼을 누를 경우 isFetching의 경우 캐시 데이터가 있더라도 출력됨 
- **prefetch한 경우 캐시데이터가 있는 상태이기 때문에 isLoading은 출력이 안되지만 isfetching은 캐시데이터 유무와 상관없이 페치일때(서버에 데이터를 가져오는 중일경우) true를 반환하므로 출력이 된다!!**
- **isLoading은 캐시데이터가 없고 페치중일 때 true를 반환함!(두가지가 충족되어야 하는 것)**

## ● mutations(변이)
- 변이는 서버에 데이터를 업데이트하도록 서버에 네트워크 호출을 실시한다.
### ✨ useMutaion
 - mutate함수를 반환하며, 이 함수는 변경 사항을 토대로 서버를 호출할 때 사용한다. // deleteMutation.mutate(post.id)
- 데이터를 저장하지 않으므로 쿼리 키는 필요하지 않다. 
- isLoading은 존재하지만 isFetching은 없다. 
- 기본값으로 재시도하지 않지만 자동 재시도를 설정할 수 있다.

### ✔️ delete
```js
import { useQuery, useMutation } from "react-query";

async function deletePost(postId) {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/postId/${postId}`,
    { method: "DELETE" }
  );
  return response.json();
}
...
// 인자로 콜백함수 지정
const deleteMutation = useMutation((postId) => deletePost(postId));
...
return (
  // useMutation이 반환하는 함수의 mutate메서드를 사용한다!
  // mutate메서드에 인자로 post.id를 전달하여, post.id가 콜백함수의 인자로 들어가 fetch함수에 전달한다.
  <button onClick={() => deleteMutation.mutate(post.id)}>Delete</button>
  // isError, isLoading, isSuccess 프러퍼티를 사용한다.
  {deleteMutation.isError && (
     <p style={{ color: "red" }}>Error deleting the post</p>
  )}
  {deleteMutation.isLoading && (
     <p style={{ color: "purple" }}>Deleting the post</p>
  )}
  {deleteMutation.isSuccess && (
     <p style={{ color: "green" }}>Post has (not) been deleted</p>
  )}
);
```

### ✔️ update
- delete와 비슷한 로직이다.

```js
async function updatePost(postId) {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/postId/${postId}`,
    { method: "PATCH", data: { title: "REACT QUERY FOREVER!!!!" } }
  );
  return response.json();
}
...
const updateMutation = useMutation((postId) => updatePost(postId));

return (
<button onClick={() => updateMutation.mutate(post.id)}>Update title</button>
  {updateMutation.isError && (
     <p style={{ color: "red" }}>Error updating the post</p>
  )}
  {updateMutation.isLoading && (
     <p style={{ color: "purple" }}>Updating the post</p>
  )}
  {updateMutation.isSuccess && (
     <p style={{ color: "green" }}>Post has (not) been updated</p>
  )}
);
```