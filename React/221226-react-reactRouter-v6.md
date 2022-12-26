## React-Router: v6에서 변경된 사항

- exact사라짐 : v6에서는 정확한 path에만 라우트가 활성화됨
  - `path=“quote/*“`: \*을 사용하여 ‘quote’ path만 있으면 해당 라우트가 활성화됨
- v6에서 더 정확한 path를 라우터가 선택하여 활성화함. 그래서 순서가 상관없어짐. 그래서 `\*`를 사용할 일이 없게 됨.
- NavLink에 activeClassName이 사라짐. 수동으로 활성화 되었는지 체크해야함
  - 👉🏻 `className={(navData) => navData.isActive ? classes.active : ‘’ }` 인수로 네비게이션의 현재 상태에 관한 정보를 얻을 수 있음(react-router가 하는일임)
- `<Redirect to={} />` 👉🏻 `<Navigate to={} />`로 변경!!

```js
<Route path="/" element={<Navigate replace to="/home" />} />
// 현재 페이지를 새페이지로 교체하려면 repalce prop을 사용!
```

- 중첩된 라우트를 사용할 경우 해당 라우트에 `<Routes></Routes>`를 감싸야함!~
  - 부모 라우트 정의에서 `\*`를 붙여야함

```js
// app.js
<Route path="/welcome/*" element={<Welcome />} />

// Welcome.js
<Routes>
  <Route path="new-users" element={<p>new user section~!</p>} />
</Routes>
 // 또는 app.js에서
<Route path="/welcome/*" element={<Welcome />} >
  <Route path="new-users" element={<p>new user section~!</p>} />
</Route>
```

- 중첩된 콘텐츠가 삽입될 위치를 React Router에게 알리기위해 `<Outlet />`를 삽입할 위치에 사용한다!
- useHistory()가 사라지고 `useNavigate()`를 사용한다.

```js
const navigate = useNavigate();
navigate("/welcome");
// replace 옵션을 사용할 경우, 두번째 인자로 객체 전달
navigate("/welcome", { replace: true });
// -1전달 시 이전 페이지로 이동, -2는 이전페이지의 전페이지로 이동됨. 1을 전달하면 다음페이지로 이동
navigate(-1);
```

- `<Prompt />`가 사라짐~ 자체적으로 구현해야함(form작성 중 해당 페이지를 나갈 경우등…)
