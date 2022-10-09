# 🖱 무한스크롤
- 사용자가 페이지의 특정 지점을 누르거나 버튼을 클릭하면 더 많은 데이터가 로딩이 된다.

## ✨ useInfiniteQuery
- 다음 쿼리가 무엇일지 추적한다.
- 두 개의 프로퍼티를 가진다. 
  - `pages`: 페이지 객체의 배열(Array)인 페이지, 페이지에 있는 각 요소가 하나의 useQuery에서 받는 데이터에 해당
  - `pageParams`: 각 페이지의 매개변수가 기록되어있음. 검색된 쿼리의 키를 추적함
- 모든 쿼리는 페이지 배열에 고유한 요소를 가지고 있고, 그 요소는 해당 쿼리에 대한 데이터에 해당, 페이지 진행되면서 쿼리도 변경됨.

### 🔗 Syntax
- pageParam: 쿼리 함수에 전달되는 매개변수
```js
useInfiniteQuery("sw-people", ({pageParam = defaultUrl}) => fetchUrl(pageParam), {옵션});
```
#### ✔️ useInfiniteQuery의 option
- `getNextPageParam` : `(lastePage, allPages) => ...` -> 다음 페이지로 가는 방식을 정의하는 함수, **lastePage, allPages을 사용해서 pageParam을 업데이트 한다.**

#### ✔️ 반환 객체의 몇 가지 프로퍼티
- `fetchNextPage` : 사용자가 더 많은 데이터를 요청할 때 호출하는 함수, **더 많은 데이터가 필요할 때 어느 함수를 실행할지를 infiniteScroll에 지시한다.**
- `hasNextPage` : getNextPageParam함수의 반환 값을 기반으로 이 프로퍼티를 useInfiniteQuery에 전달하여 마지막 쿼리의 데이터를 어떻게 사용할지 지시한다. **수집할 데이터가 더 있는지를 결정하는 불리언값**
  - undefined인 경우 더 이상 데이터가 없다는 것
  - useInfiniteQuery에서 반환 객체와 함께 반환된 경우 hasNextPage는 거짓이 됨
- `isFetchingNextPage` : useInfiniteQuery는 다음 페이지를 가져오는지 아니면 일반적인 페칭인지 구별할 수 있음

### 🔗 Flow
![](https://velog.velcdn.com/images/zooyaho/post/384ce052-1d04-40b4-820d-8b81ebeaa52e/image.png)

`const {data} = useInfinityScroll( ({pageParam = defaultUrl}) => ...)`
- data.pages[0] : 첫번째 page데이터를 반환한다. `({pageParam = defaultUrl}) => ...` 가 반환을 하는 것
- 데이터를 반환하고 React Query가 `getNextPageParam`을 실행한다.
- hasNextPage가 있다면 fetchNextPage 함수를 트리거하는 사용자 행동을 했을때(사용자가 더 많은 데이터를 요청했을 때)
- 이때 React Query가 `({pageParam = defaultUrl}) => ...`를 실행한다. data.pages[1]로 업데이트한다.
- 만약 2페이지만 있다고 가정했을 때, getNextPageParam을 실행할 때NextPageParam은 정의되지 않는다.
- hasNextPage는 pageParam이 undefined가 되기때문에 false가 된다.
- hasNextPage가 거짓이라는 것은 작업이 완료되었다는 것이다.

### 🔗 InfiniteScroll
``` js
import InfiniteScroll from "react-infinite-scroller";
```
- useInfiniteQuey와 호환이 잘 되어 사용한다.

#### 두개의 프롭
- `loadMore={fetchNextPage}` : 데이터가 필요할 때 불러와 hasNextPage 함숫값을 이용한다.
- `hasMore={hasNextPage}` : useInfiniteQuery에서 나온 객체를 해체한 값을 이용한다.

### 🔗 예제
✔️ API JSON
![](https://velog.velcdn.com/images/zooyaho/post/cc7f0fb8-92c6-4401-b350-456bb870575d/image.png)

```js
import InfiniteScroll from "react-infinite-scroller";
import { useInfiniteQuery } from "react-query";
import { Person } from "./Person";

const initialUrl = "https://swapi.dev/api/people/";
const fetchUrl = async (url) => {
  const response = await fetch(url);
  return response.json();
};

export function InfinitePeople() {
  const { data, fetchNextPage, hasNextPage } = useInfiniteQuery(
    "sw-people",
    ({ pageParam = initialUrl }) =>
      fetchUrl(
        // hasNextPage함수는 getNextPageParam이 undefined를 반환하는지에 따라 결정된다.
        pageParam
      ),
    // lastPage.next이 없다면 undefined를 설정해 hasNextPage가 flase를 반환하게 한다.
    { getNextPageParam: (lastPage, allPage) => lastPage.next || undefined }
  );

  return (
    <InfiniteScroll hasMore={hasNextPage} loadMore={fetchNextPage}>
      {data.pages.map((pageData) => {
        return pageData.results.map((person) => {
          return (
            <Person
              key={person.name}
              name={person.name}
              hairColor={person.hair_color}
              eyeColor={person.eye_color}
            />
          );
        });
      })}
    </InfiniteScroll>
  );
}
```
🚨 [오류] : fetchUrl(pageParam)이 완료 되기 전에 map으로 데이터를 출력하기 때문에 오류가 발생한다.

#### ✔️ loading, error
```js
const { data, fetchNextPage, hasNextPage, isLoading, isError, error } = useInfiniteScroll( ({pageParam = initailUrl}) => { fetchUrl(pageParam) }, { getNextPageParm: (lagePage, allPage) => lagePage.next || undefined });
      
if (isLoading)
    return (
      <h3 style={{ position: "fixed", top: "5px", right: "5px" }}>
        Loading...
      </h3>
    );
if (isError)
  return (
    <h3 style={{ position: "fixed", top: "5px", right: "5px" }}>
      🚨 Error! {error.toString()}
    </h3>
  );
```
#### ✔️ 데이터를 수집하고 있다는 피드백 주기

```js
const { data, ..., isFetching } = useInfiniteScroll(...);
                                                    
if (isFetching)
    return (
      <h3 style={{ position: "fixed", top: "5px", right: "5px" }}>
        Fetching...
      </h3>
    );                                                   
```
🚨 [오류] : 조기 반환이 되어 무한스크롤을 할경우 스크롤이 상단으로 이동한다.
💡 [해결] : 컴포넌트의 jsx반환에서 조건문을 작성한다!

```js                                                   
  return (
    <>
      {isFetching && (
        <h3 style={{ position: "fixed", top: "5px", right: "5px" }}>
          Fetching!!
        </h3>
      )}
      <InfiniteScroll hasMore={hasNextPage} loadMore={fetchNextPage}>...
  );
```

## 🔎 양방향 스크롤
- 양방향 스크롤은 전,후 페이지 fetching이 필요하다.
- 그래서 이전 페이지에 대해서도 동일한 기능을 수행할 수있다!
