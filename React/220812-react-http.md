22.08.12 - TIL

> [참고] Udemy - React 완벽 가이드 with Redux, Next.js, TypeScript

# ⚛︎ React에서 HTTP 요청 보내기

- 리액트 앱 코드 내부에서 **데이터베이스에 직접적으로 통신하면 보안상 위험함.**
- 다른 서버에서 실행되는 백앤트 어플리케이션을 이용하여 데이터베이스와 통신해야 함 -> 데이터베이스의 인증정보가 백앤드 서버에 있으므로 안전하게 저장할 수 있다.
- 리액트는 백앤드 서버(백앤드 API)라고 불리는 서로 다른 URL로의 요청을 전송하는 서버와 통신하게 된다.
- HTTP 요청에 대한 API를 말할 때, 보통 REST 또는 GraphQL API를 말하게 된다. 이 두개는 서버가 데이터를 노출하는 방식에 대한 서로 다른 표준이다.

## 1. 로딩 및 데이터 State 처리하기

: 스타워즈 API를 불러와 버튼 클릭 시 리스트 출력  
: 여기서 api 호출은 Get요청 방식으로 진행된다.

![](https://velog.velcdn.com/images/zooyaho/post/92e4c52e-9a69-49f2-b76b-16f3b718af4b/image.png)

👾 App.js

```js
function App() {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);

  async function fetchMoviesHandler() {
    setIsLoading(true);
    const response = await fetch("https://swapi.dev/api/films/");
    const data = await response.json();

    const transformedMovies = data.results.map((movieData) => {
      return {
        id: movieData.episode_id,
        title: movieData.title,
        openingText: movieData.opening_crawl,
        releaseDate: movieData.release_date,
      };
    });
    setMovies(transformedMovies);
    setIsLoading(false);
  }

  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>
        // api요청 완료 후 리스트 개수 있을 경우
        {!isLoading && movies.length > 0 && <MoviesList movies={movies} />}
        // api요청 완료 후 리스트 개수가 없을 경우
        {!isLoading && movies.length === 0 && <p>Found no movies.</p>}
        // api요청 완료 전{isLoading && <p>Loading...</p>}
      </section>
    </React.Fragment>
  );
}
```

👉🏻 로딩 결과에 따른 화면 출력을 state로 관리함.

## 2. HTTP오류 처리하기

- fetch api는 에러 상태코드를 실제 에러로 취급하지 않음!! (오류코드를 받아도 기술적인 오류로 처리하지 않음)
- 가져오지 못한 데이터로 어떤 작업을 하려고 할때만 오류가 발생한다!
- 상태오류코드를 받았을 때 실제 오류가 발생하게끔 처리해야함!
- axio api는 에러상태코드를 에러로 판단하지만 fetch api는 그런 기능이 없음.
- `response.ok` : 요청이 성공적인지 그렇지 않은지를 불린값으로 반환함. 이것을 사용하여 자체적인 오류를 만들어 표시하면 됨!!

🚨 **오류 종류**

- `401, 403, 404응답` : 기술적으로는 성공적으로 응답을 받았으나 응답에 오류상태 코드가 포함되어 있어서 오류문자를 받아 발생
- `5xx` : 서버에 오류가 있을 때 발생

👾 App.js

```js
function App() {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  // 초기에는 오류가 없기 때문에 초기값을 null로 설정
  const [error, setError] = useState(null);

  async function fetchMoviesHandler() {
    setIsLoading(true);
    // 이전에 받았던 error를 초기화
    setError(null);

    try {
      const response = await fetch("https://swapi.dev/api/films/");
      // 오류 상태코드 발생 시 오류 생성!!
      if (!reponse.ok) {
        throw new Error("에러 발생");
      }
      const data = await response.json();

      const transformedMovies = data.results.map((movieData) => {
        return {
          id: movieData.episode_id,
          title: movieData.title,
          openingText: movieData.opening_crawl,
          releaseDate: movieData.release_date,
        };
      });
      setMovies(transformedMovies);
    } catch (error) {
      // try블록에서 발생할 수 있는 잠재적인 오류들을 catch로 포착
      // error state에 에러메세지 저장
      setError(error.message);
    }
    // error가 발생했던 간에 로딩은 끝난 상태여야 함.
    setIsLoading(false);
  }

  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>
        {!isLoading && movies.length > 0 && <MoviesList movies={movies} />}
        {!isLoading && movies.length === 0 && !error && <p>Found no movies.</p>}
        // error발생 시 에러메세지 출력
        {!isLoading && error && <p>{error}</p>}
        {isLoading && <p>Loading...</p>}
      </section>
    </React.Fragment>
  );
}
```

👾 return 리팩토링

```js
let content = <p>Found no movies.</p>;

if (movies.length > 0) {
  content = <MoviesList movies={movies} />;
}
if (error) {
  content = <p>{error}</p>;
}
if (isLoading) {
  content = <p>Loading...</p>;
}

return (
  <React.Fragment>
    <section>
      <button onClick={fetchMoviesHandler}>Fetch Movies</button>
    </section>
    <section>{content}</section>
  </React.Fragment>
);
```

👉🏻 content만 비교하여 렌더링하게 됨!

👾 fetchMoviesHandler함수 리팩토링

```js
function App() {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  const fetchMoviesHandler = useCallback( async () => {
    setIsLoading(true);
    setError(null);
    ...

  }, []);
  // 이 함수는 외부 의존성이 없으므로 의존성배열을 빈값으로 둔다!

  // fetchMoviesHandler는 함수이므로 app이 재평가 될때마다 메모리에 새롭게 저장됨(무한루프) -> 함수 재정의 방지를 위해 useCallback훅을 사용한다.
 // app컴포넌트 재평가 시 함수의 호출을 방지하고, 필요한 경우(버튼 클릭)에만 호출하기 위함!
  useEffect(() => {
    fetchMoviesHandler();
  },[fetchMoviesHandler]);
...
}
```

👉🏻 `useEffect(()=>{fetchMoviesHandler();},[]);` : 의존성 배열을 빈값으로 둠으로써 최초 로딩 시 api호출, app이 재렌더링 시 재호출 되는것을 방지함. 하지만 의존성 배열에 의존성을 표시해야 좋은 코드이다.  
👉🏻 fetchMoviesHandler함수 재정의 방지를 위해 useCallback훅을 사용한다.

## 3. Post 요청 보내기

: 사용자를 추가하는 경우 **서버에 데이터를 보내는 작업을 한다.**  
: `Firebase`는 코드 작성 없이 사용 가능한 백엔드 서비스이며, 우리의 요청을 주고 받을 수 있으며, Post 요청 작업을 수행할 수 있다.(완전한 REST API를 제공)  
: `fetch` : 기본적으로 Get요청을 보내지만, Post요청으로 사용하면 데이터를 전송하는데에도 사용가능함. 두번째 인자에 객체를 생성

👾 영화 추가하는 버튼 클릭 시 addMovieHandler호출하여 Firebase에 해당 영화 추가됨.

```js
async function addMovieHandler(movie) {
  const response = await fetch(
    "https://react-http-6b4a6.firebaseio.com/movies.json",
    {
      method: "POST", // dafault 'GET'
      body: JSON.stringify(movie), // JSON형태로 변경
      headers: {
        "Content-Type": "application/json",
      },
    }
  );
  const data = await response.json();
  console.log(data);
}
```

👉🏻 일반적으로도 그렇지만 Firebase에서 **POST요청을 보내면 데이터베이스에서 리소스가 생성됨.**  
👉🏻 `body옵션` : **저장할 리소스 작성**  
👉🏻 `headers옵션` : 객체를 지정, Firebase는 headers가 필요없지만 요청을 받는 대다수의 api는 이러한 헤더를 필요로 함. 이 헤더를 통해 **어떤 컨텐츠가 전달되는지 알 수 있음!**  
👉🏻 Firebase는 자동으로 컨텐츠에 아이디를 생성하고, id를 key로 갖고 추가된 영화 데이터가 객체형태로 value에 저장됨!

> [참고] Udemy - React 완벽 가이드 with Redux, Next.js, TypeScript
