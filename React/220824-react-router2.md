## ✔️ React-router-dom 버전5

### ● 라우트 작업하기

- `<switch>`: 위에서 아래로 읽어들여 path와 가장 먼저 일치하는 항목을 찾으면 switch로 인해 중단되고 일치한 첫번째 라우트를 렌더링
- `exact={true}`: **정확한 경로에만 해당 화면이 이동함.**

👾 App.js

```js
<switch>
  <Route path="/">
    <Home />
  </Route>
  <Route path="/movie">
    <Movie />
  </Route>
  <Route path="/movie/:id">
    <MovieDetail />
  </Route>
</switch>
```

👉🏻 url이 `/movie`일 경우 <Movie />만 활성화 시킴  
👉🏻 exact를 사용하면 정확한 경로에만 해당 라우트가 렌더링되게 함.
ex) `<Route exact path="/movie">`

### ● 중첩 라우트 작업하기

- 원하는 곳 어디에서나 라우트 정의가 가능하다.

👾 App.js

```js
<switch>
  <Route path="/welcome">
    <Welcome />
  </Route>
  ...
</switch>
```

👾 Welcome.js

```js
<section>
  <h1>The Welcome Page</h1>
  <Route path="/welcome/new-user">
    <p>welcome new user!!!</p>
  </Route>
</section>
```

👉🏻 url이 `/welcome`일 경우`<h1>The Welcome Page</h1>`만 화면에 렌더링 되고,  
👉🏻 url이 `/welcome/new-user`일 경우 `<h1>The Welcome Page</h1><p>welcome new user!!!</p>`가 렌더링 된다!

### ● 사용자 redirect

- url이 route에 설정한 path들 중 일치하는 것이 없을 경우, 이동할 경로를 설정하여 리디렉션한다.
- `<Redirect to="path" />`: 사용자를 path에 설정한 url로 리디렉션함

👾 App.js

```js
  <switch>
    <Route path="/" exact>
      <Redirect to="/welcome" >
    </Route>
    <Route path="/welcome">
	  <Welcome />
	</Route>
	...
  </switch>
```

👉🏻 url이 `/`일 경우`/welcome`으로 변경하여 해당 화면을 렌더링한다.
