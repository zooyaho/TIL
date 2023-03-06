# 라우팅(Routing): SPA구축하기

- url과 대화하는(interact) 상태여야 위치한 페이지를 나타내는 URL을 다른 사용자와 공유할 수 있다.
- `라우팅이란` 브라우저에서 JS로 URL의 변경 사항을 관리하고 동일한 싱글 페이지 애플리케이션 상에 URL이 변경되거나 해당 URL을 기반으로 화면에 로드하게 하는 것이다.
- `vue-router`패키지를 사용하여 라우팅 기능을 구현한다.

## createRouter

- main.js에서 라우트를 생성한다.
- 구성 객체에 두가지 옵션을 추가한다.
- router상수를 `use메서드`를 사용하여 등록한다.
- `use메서드`: 내장 메서드로 Vue앱에 서드파티 패키지와 다른 기능을 연결해주는 역할이다.

### routes 옵션

- `routes`: 지원하고자 하는 여러 URL을 등록한다.
- 어떤 URL에 대해 어떤 Vue 컴포넌트가 로드되었는지를 알린다.
- `하나의 라우트 객체 요소를 가지는 배열`을 할당한다.
- 객체의 옵션으로 `path`, `component`을 사용하며 URL에서 도메인의 뒷부분에 해당(path), 어떤 라우터가 어떤 컴포넌트를 로드(component)해야 하는지 알려주는 역할이다.

### history 옵션

- `history`: 라우터에게 본 앱의 라우팅 히스토리 관리 방법을 알려준다. `해당 페이지가 브라우징 히스토리에 등록되도록 하는 방식을 설정한다.`
- `createWebHistory()`: 사용자가 어디로부터 왔는지에 대한 정보 등을 알려주는 것을 브라우저 내장 메커니즘이 했었는데 이 기능을 라우터에게 지원하기 위해 지정하는 것이다.

## `<router-view>` 컴포넌트

- 라우트에 정의한 컴포넌트가 어디에 렌더링 되어야 하는지 알려줘야한다.
- 컴포넌트가 출려될 위치에 `<router-view></router-view>` 컴포넌트를 추가한다.
- 이제 url로 해당 컴포넌트가 출력될 수 있다.

👾예제  
● main.js

```js
import { createRouter, createWebHistory } from "vue-router";
import TeamsList from "./components/teams/TeamsList.vue";
import UsersList from "./components/users/UsersList.vue";

import App from "./App.vue";

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: "/teams",
      component: TeamsList,
    },
    {
      path: "/users",
      component: UsersList,
    },
  ],
});
const app = createApp(App);

app.use(router);

app.mount("#app");
```

● App.vue

```vue
<template>
  <the-navigation @set-page="setActivePage"></the-navigation>
  <main>
    <!-- <component :is="activePage"></component> -->
    <router-view></router-view>
  </main>
</template>
```

## `<router-link>` 컴포넌트

- 브라우저 기본값으로 다른 페이지가 로드되는 것(자체적으로 다른 HTML파일을 로드하는 것)을 막고 Vue라우터를 통해 `사용자가 클릭한 링크를 분석하여 알맞은 컴포넌트를 로드하며 URL을 업데이트`하는 앵커 태그이다.
- 내부적으로 slot을 사용하기 때문에 텍스트나 다른 요소를 작성할 수 있다.
- `to="경로"`속성을 사용하여 클릭시 이동할 경로를 설정한다.

### 활성 링크 스타일링하기 - router-link-active, router-link-exact-active

- `router-link-active`는 특수 CSS 스타일로 활성화된 앵커 태그에 자동으로 추가된다. 이 클래스를 사용하여 스타일링을 할 수 있다.
- `router-link-exact-active`는 중첩 라우팅이 있을 때 `현재 경로와 완벽하게 일치하는 내비게이션 항목에만 적용된다.`

```css
a:hover,
a:active,
a.router-link-active {
  color: #f1a80a;
  border-color: #f1a80a;
  background-color: #1a037e;
}
```

- 클래스 이름을 변경할 수도 있는데 crateRouter 구성 객체에 `linkActiveClass: 변경할 클래스명`, `linkExactActiveClass: 변경할 클래스명`으로 설정하면 된다.

## 버튼으로 페이지 이동하기(라우팅)

- 컴포넌트 내장객체에서 사용할 수 있는 `this.$router.push()`를 사용한다. 라우팅 히스토리에 새 라우트를 푸시하는 것이다(브라우저에 저장되는 라우팅 메모리에 새 라우트를 추가하는 것) -`this.$router.back()`, `this.$router.forward()`로 뒤로가기 및 앞으로 가기가 된다.

## 동적 라우트 매개변수 사용하기 - $route

- createRouter의 구성객체의 routes에서 path를 `test/:id`형식으로 추가한다.
- created: 컴포넌트 생성 시 모든 데이터가 사용 가능 상태이며 화면에 표시 되기전에 호출된다. 그러면 created를 통해 주입된 데이터 뿐만 아니라 라우팅 데이터도 액세스할 수 있다.
- `$route`로 `params, path`등 사용하여 path관련 데이터를 얻을 수 있다.
- 버튼에 동적 라우트 매개변수값으로 이동하려면 to에 바인딩한 상태로 path설정을 하면 된다.

```vue
<template>
  <li>
    ...
    <!-- <router-link :to="'/teams/' + id">View Members</router-link> -->
    <router-link :to="teamMembersLink">View Members</router-link>
  </li>
</template>
<script>
export default {
  props: ['id', 'name'],
  computed(){
    teamMembersLink() {
      return '/teams/' + this.id;
    }
  }
}
</script>
```

```vue
...
<script>
...
created() {
    const teamId = this.$route.params.teamId; // $route 사용하여 라우트 매개변수 값 가져오기
    ...
  },
</script>
```

## 감시자(watcher)로 매개변수 데이터 업데이트하기 - watch: {}

- 동적 매개변수로 로드된 페이지에서 다른 페이지로 갈 때, 즉 매개변수의 다른 값을 가진 페이지로 간다면(사실상 같은 페이지로 이동) 문제가 생긴다.
- ⭐️ 예를 들어서 team/t1 페이지에서 team/t2 페이지로 이동하는 버튼을 클릭 했을 때 해당 페이지로 이동하지 않는다.(url은 바뀌지만 데이터가 바뀌지 않음!!) => 왜냐하면 캐시 때문인데 url이 바뀌었다고 created()가 다시 호출되지 않기 때문이다.
- watch객체를 사용하여 $route가 변하는걸 감시한다!!

```js
export default {
  methods: {
    loadTeamMembers(route) {
      const teamId = route.params.teamId;
      ...
    },
  },
  created() {
    this.loadTeamMembers(this.$route);
  },
  watch: { // 라우트에 생기는 변화를 감시 중~!
    $route(newRoute) { // 최신 $route값이 인수로 들어옴!!
      this.loadTeamMembers(newRoute);
    },
  },
}
```

## 매개변수를 프로퍼티로 전달하기

- props: true로 하여 props로 매개변수 값을 받는걸 허락한다.
- ⭐️ 매개변수 값이 프로퍼티로서 전달된다!! (ex. :teamId)

```js
{
  path: '/teams/:teamId',
  component: TeamMembers,
  props: true,
},
```

## 리디렉션 및 CatchAll 라우트

- redirection은 redirect, alias프로퍼티를 사용한다.

```js
routes: [
    // '/'이 '/home'로 변경된다.(url 변경됨!!)
    { path: '/', redirect: '/home' },
    // '/'이여도 TeamsList화면으로 출력된다.(url변경은 되지 않음!)
    { path: '/teams', component: TeamsList, alias: '/'},
    ...
  ],
```

- 지정하지 않는 경로로 접속하는 경우 `path: '/:notFound(.*)'`을 추가한다. 🔥 마지막에 작성해야 다른 라우트를 덮어쓰지 않는다!!

```js
routes: [
    // '/'이 '/home'로 변경된다.(url 변경됨!!)
    { path: '/', redirect: '/home' },
    // '/'이여도 TeamsList화면으로 출력된다.(url변경은 되지 않음!)
    { path: '/teams', component: TeamsList, alias: '/'},
    { path: '/:notFound(.*)', redirect: '/home'},
    ...
  ],
```
