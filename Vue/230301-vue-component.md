# 컴포넌트

## 컴포넌트 생성과 사용법

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

## props사용하기

- 자식 컴포넌트에 porps를 전달하기 위해서 속성으로 `케밥케이스 표기법(-)`을 사용해야한다.
- 1️⃣ 자식컴포넌트에서 부모 컴포넌트에게 받은 porps는 `배열 형식의 각 요소에 key값(카멜케이스-문자열형식)`을 작성한다. 이때 methods 프로퍼티에서 사용하려면 `this로 접근`하고 template에서 접근하려면 `보간법에 변수만 작성`하여 사용한다.
- 2️⃣ 두번째 방법으로는 props프로퍼티에 `객체 형태`로 작성한다. 각각의 프로퍼티 값들에 타입을 작성한다. 구체적인 설정을 하려면 프로퍼티 값을 객체 형태로 지정하여 `type, required, default, validator` 등 속성을 추가한다.
- `type` : String, Number, Boolean, Array, Object, Date, Function, Symbol 이 있다
- ⭐️ type이 Boolean일 경우 부모컴포넌트에서 props로 전달 시 `v-bind:`로 설정 후 넘겨야 한다.
  `ex. v-vind:id-favorite="true"` 바인딩을 하지 않을 경우 문자열로 보내게 된다!

● App.vue - 부모 컴포넌트

```js
...
<ul>
  <friend-contact
    name="Manuel Lorenz"
    phoneNumber="0123 45678 90"
    emailAddress="manuel@localhost.com"
  ></friend-contact>
  <friend-contact
    name="Manuel Lorenz"
    phoneNumber="010 1234 5678"
    emailAddress="manuel@localhost.com"
  ></friend-contact>
</ul>
...
```

1️⃣ 예시  
● FriendContact.vue - 자식 컴포넌트

```js
<template>
  <li>
    <h2>{{ name }}</h2>
    <button @click="toggleDetails">
      {{ detailsAreVisible ? "Hide" : "Show" }} Details
    </button>
    <ul v-if="detailsAreVisible">
      <li>
        <strong>Phone:</strong>
        {{ phoneNumber }}
      </li>
      <li>
        <strong>Email:</strong>
        {{ emailAddress }}
      </li>
    </ul>
  </li>
</template>

<script>
export default {
  props: ["name", "phoneNumber", "emailAddress"],
  ...
}
</script>
```

2️⃣ 예시  
● FriendContact.vue - 자식 컴포넌트

```vue
<script>
export default {
  props: {
    name: String,
    phoneNumber: {
      type: String,
      required: true, // 선택사항 옵션 추가, 값이 안들어와도 error는 발생시키지 않지만 개발자 도구로 경고해서 알려줌. => 개발에 도움을 준다.
    },
    isFavorite: {
      type: String,
      required: false,
      default: '0', // default값 지정
      validator: (value) => {
        // 유효한 값인지 아닌지를 확인하는 bool값을 리턴 => 여기도 또한 false를 뱉을 경우 개발자도구에서 경고로 알려줌
        return value === '1' || value === '0';
        // isFavorite의 값은 0과 1만 가지게 된다.
      }
    }
  },
  ...
}
</script>
```

## 단방향 플로우

- `props로 받은 값들은 불변`해야한다. vue는 단방향 플로우를 가지고 있어 자식 컴포넌트에 보낸 props값들은 부모컴포넌트에서만 변경해야 에러가 발생하지 않는다.
- 데이터를 변경하려면 두 가지 방법이 있다.  
  @ 첫번째는 자식 내부의 초기 데이터로 취급해 자식에서 직접 변경하는 것이다.
  @ 두 번째는 부모에게 변경하고 싶다고 알리는 것

### 1️⃣ 자식 내부의 초기 데이터로 취급해 자식에서 직접 변경

- data(){}에서 새로운 프로퍼티를 만들어 props로 받은 값을 할당한다.

```js
<script>
export default {
  props: ["name", "phoneNumber", "emailAddress", "isFavorite"],
  data() {
    ...
    friendIsFavorite: this.isFavorite,
  }
}
</script>
```

### 2️⃣ 부모에게 변경하고 싶다고 알리는 것 - $emit() => 양방향 바인딩

- `this키워드`로 호출할 수 있는 내장 메서드인 `$emit()`을 사용하여 부모 컴포넌트에서 수신할 수 있는 커스텀 이벤트를 발생 시켜줍니다.
- $emit(커스텀 이벤트명, 이벤트함수에 전달될 데이터,,): 최소한 하나의 인수인 커스텀 이벤트의 이름을 요구한다.`(케밥케이스로 작성)`

#### ⭐️ emits

- props는 컴포넌트가 수신하는 프로퍼티를 정의했고, `emits에서는 컴포넌트가 어떠한 시점에 발생시킬 커스텀 이벤트를 정의한다.`
- 정의하면 이 컴폰넌트의 `작동 방식`과 `이떤 이벤트를 수신하는지` 등을 다른 개발자가 명확하게 알 수 있다.
- 기본 `emits: ["toggle-favorite"]`
- 객체로 작성하면 더 자세한 구정을 제공할 수 있다. 부모컴포넌트에서 함수를 정의한걸 자식컴포넌트에서 알려주는 식 유효성 검사도 할 수 있다. => 커스텀 이벤트 사용을 표시하는 것

👾 예시 - 토글 버튼을 클릭하여 좋아요 상태 변경하기
● App.vue

```js
<templata>
  <friend-contact
    v-for="friend in friends"
    :key="friend.id"
    :id="friend.id"
    {/* ... */}
    :is-favorite="friend.isFavorite"
    @toggle-favorite="toggleFavoritesStatus"
  ></friend-contact>
</template>

<script>
  export default {
  data() {
    return {
      friends: [
        {
          id: "manuel",
          name: "Manuel Lorenz",
          phone: "0123 45678 90",
          email: "manuel@localhost.com",
          isFavorite: true,
        },
        {
          id: "julie",
          name: "Julie Jones",
          phone: "0987 654421 21",
          email: "julie@localhost.com",
          isFavorite: false,
        },
      ],
    };
  },
  methods: {
    toggleFavoritesStatus(friendId) {
      const identifiedFriend = this.friends.find(
        (friend) => friend.id === friendId
      );
      identifiedFriend.isFavorite = !identifiedFriend.isFavorite;
    },
  },
};
</script>
```

● FriendContact.vue - 자식 컴포넌트

```js
<templata>
...
<h2>{{ name }} {{ isFavorite ? "(Favorite)" : "" }}</h2>
<button @click="toggleFavorite">toggle favorite</button>
</template>

<script>
  export default {
  props: ["id", "name", "phoneNumber", "emailAddress", "isFavorite"],
  ...
  // emits: ["toggle-favorite"],
  emits: {
    "toggle-favorite": function (id) {
      if (id) return true;
      else {
        console.warn("id를 찾을 수 없습니다.");
        return false;
      }
    },
  },
  methods: {
    toggleFavorite() {
      this.$emit("toggle-favorite", this.id); // id를 인자로 보내어 해당 상태값 변경
    },
  },
};
</script>
```

## 동적 프로퍼티 값 사용하기

- v-for속성을 사용하여 동적 바인딩한다.

● App.vue - 부모 컴포넌트

```vue
<friend-contact
  v-for="friend in friends"
  :key="friend.id"
  :name="friend.name"
  :phone-number="friend.phone"
  :email-address="friend.email"
  :is-favorite="friend.isFavorite"
></friend-contact>
```

## provide(제공), inject(삽입)

- 한곳에 데이터를 제공하고 삽입, 즉 해당 데이터를 다른 곳에서 사용할 수 있는 패턴이다.
- ⭐️ provide 프로퍼티를 사용하여 제공하고 inject 프로퍼티를 사용하여 사용한다.
- provide 함수에 data메서드의 프로퍼티를 최신값으로 사용하여 추가할 수 있다.

● App.vue - 부모컴포넌트

```vue
<script>
export default {
  provide() {
    return {
      topics: this.topics,
      selectTopic: this.activateTopic,
    };
  },
  methods: {
    activateTopic(topicId) {
      this.activeTopic = this.topics.find((topic) => topic.id === topicId);
    },
  },
};
</script>
```

● 자식 컴포넌트

```vue
<template>
  <ul>
    <knowledge-element
      v-for="topic in topics"
      :key="topic.id"
      :id="topic.id"
      :topic-name="topic.title"
      :description="topic.description"
    ></knowledge-element>
  </ul>
</template>

<script>
export default {
  inject: ["topics"],
};
</script>
```

● 또 다른 자식 컴포넌트 - 커스텀 이벤트 대신 inject사용하여 컴포넌트 통신

```vue
<template>
  <li>
    <h3>{{ topicName }}</h3>
    <p>{{ description }}</p>
    <button @click="selectTopic(id)">Learn More</button>
  </li>
</template>

<script>
export default {
  inject: ["selectTopic"],
  props: ["id", "topicName", "description"],
};
</script>
```
