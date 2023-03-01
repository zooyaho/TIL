## 컴포넌트

### 컴포넌트 생성과 사용법

- 컴포넌트는 기본적으로 커스텀 HTML 요소이다
- 새로운 앱이므로 자체 템플릿이 필요하다. 부모 템플릿에 종속되어있을 뿐 독립적인 뷰앱(미니앱)이다.
- 생성: `뷰앱.component(식별자, 구성객체)`
  ● 식별자(컴포넌트 식별자): 항상 대시 기호를 포함한 식별자를 사용해야 한다.  
  👉🏻 공식 내장 HTML요소와 충돌되는 일을 막기위해 `두단어`로 구성되어야한다.  
  ● 객체: createApp에 전달한 것과 같은 구성객체, 컴포넌트가 `캡슐화`되어 있으므로 충돌이 발생하지 않는다.
- 사용: 식별자를 사용해서 공식 HTML 요소인것처럼 사용한다.

👾 기본 예시

- index.html

```html
<section id="app">
  <ul>
    <friend-contact />
  </ul>
</section>
```

- app.js

```js
const app = Vue.createApp({
  data() {
    return {
      friends: [
        {
          id: "manuel",
          name: "Manuel Lorenz",
          phone: "01234 5678 991",
          email: "manuel@localhost.com",
        },
        {
          id: "julie",
          name: "Julie Jones",
          phone: "09876 543 221",
          email: "julie@localhost.com",
        },
      ],
    };
  },
});

app.component("friend-contact", {
  template: `
  <li>
    <h2>{{ friend.name }}</h2>
    <button @click="toggleDetails">
      {{ detailsAreVisible ? 'Hide' : 'Show'}} Details
    </button>
    <ul v-if="detailsAreVisible">
      <li><strong>Phone:</strong>{{ friend.phone }}</li>
      <li><strong>Email:</strong>{{ friend.email }}</li>
    </ul>
  </li>
  `,
  data() {
    return {
      detailsAreVisible: false,
      friend: {
        id: "julie",
        name: "Julie Jones",
        phone: "09876 543 221",
        email: "julie@localhost.com",
      },
    };
  },
  methods: {
    toggleDetails() {
      this.detailsAreVisible = !this.detailsAreVisible;
    },
  },
});

app.mount("#app");
```
