# vuex

- 전역상태를 관리하기 위한 라이브러리이다.
- `상태`는 데이터로 변환될 수 있는데, 애플리케이션이 필요로하는 데이터와 사용자가 화면에서 무엇을 보는지에 영향을 주는 데이터를 말한다. 일종의 반응형 데이터이다. 변경될 수 있으며 변경함으로써 화면상의 무언가를 트리거하는 데이터이다.
- createStore()를 사용하여 store를 생성하고, $store을 사용하여 데이터를 사용한다.

## 폴더 구조

![](https://velog.velcdn.com/images/zooyaho/post/79af4daa-0271-413e-8244-bf65b41c0e6d/image.png)

## 생성과 사용

● main.js - 생성

```js
import { createStore } from "vuex";
const store = createStore({
  state() {
    return {
      counter: 0,
    };
  },
});

const app = createApp(App);
app.use(store); // use메서드를 사용하여 App에 등록한다.
```

● App.vue - 사용

```vue
<template>
  <base-container title="Vuex"></base-container>
  <h2>{{ $store.state.counter }}</h2>
  <button @click="addOne">add 1</button>
</template>

<script>
import BaseContainer from "./components/BaseContainer.vue";

export default {
  components: {
    BaseContainer,
  },
  methods: {
    addOne() {
      this.$store.state.counter++;
    },
  },
};
</script>
```

## 데이터를 변경하는 방법 - mutations

- 직접 변경을 하는게 아닌 변형을 트리거하여 상태를 변경하는 것이 올바른 메커니즘이다.
- store에 객체 형태인 mutations 프로퍼티를 추가한다.
- mutations프로퍼티에 추가한 `함수에는 인수로 현재상태의 state`가 들어온다. 객체 형태인 `payload`를 사용하여 데이터를 인수로 받아올 수 있다.
- `commit()`메서드를 사용하여 변형의 이름을 인수로 추가한다.

👾 예제  
● main.js

```js
const store = createStore({
  state() {
    return {
      counter: 0,
    };
  },
  mutations: {
    increment(state) {
      state.counter++;
    },
    increse(state, payload) {
      // payload사용하여 값 변경
      state.counter += payload.value;
    },
  },
});
```

● App.vue

```js
export default {
  methods: {
    addOne() {
      // this.$store.commit('increse', { value: 10 });
      this.$store.commit({ type: "increse", value: 10 }); // 또 다른 방법
    },
  },
};
```

## 데이터를 얻는 또 다른 방법 - getters

- state를 여러개 출력하고자 할때 하나는 다른 값으로 계산되어 출력되도록 하는것! state값이 변경된 값으로 바뀌는 것이 아니다!!(🔥 원형값을 변경하지 않음!!!)
- store에 객체 형태인 getters 프로퍼티를 추가한다.
- getters 프로퍼티에 추가한 함수에는 `인수로 현재 상태의 state와 getters`가 들어온다.
- getters인수는 여기서 계산하려는 값이 다른 게터의 결과에 따라 달라지는 경우 게터에 다른 게터를 넣는것이 유용할 때 사용한다.

👾 예제  
● main.js

```js
const store = createStore({
  state() {
    return {
      counter: 0,
    };
  },
  getters: {
    finalCounter(state) {
      console.log("현재 state counter: ", state.counter); // 원형값은 변경되지 않는다.
      return state.counter * 2;
    },
    normalizedCounter(_state, getters) {
      // const finalCounter = state.counter * 2;
      const finalCounter = getters.finalCounter;
      if (finalCounter < 0) return 0;
      else if (finalCounter > 100) return 100;
      else finalCounter;
    },
  },
});
```

● TheCounter - 사용

```vue
<template>
  <h2>{{ counter }}</h2>
</template>

<script>
export default {
  computed: {
    counter() {
      return this.$store.getters.finalCounter; // 포인터만 전달하여 사용한다.
    },
  },
};
</script>
```

## 액션(Actions)로 비동기 작업 수행하기

- 변형은 동기식으로 작동하는데 비동기 작업을 위해서는 actions를 사용해야한다.
- 컴포넌트는 액션을 트리거하고 이는 변형을 커밋한다.
- store의 구성객체에 객체 형태인 actions를 추가한다.
- `변형(mutaions)에 추가한 함수와 똑같은 이름의 함수를 actions에 추가`한다.
- 함수에 인수로 객체형태인 `context`를 받는다. context는 호출할 수 있는 `commit메서드`를 가지고 있어 `mutations에 추가한 함수를 호출`한다!!
- `dispatch`를 통해 actions의 함수를 호출한다. 문법은 commit과 똑같다.

👾 예제  
● main.js

```js
const store = createStore({
  state() {
    return {
      counter: 0,
    };
  },
  mutations: {
    increment(state) {
      state.counter++;
    },
    increase(state, payload) {
      state.counter += payload.value;
    },
  },
  actions: {
    // 비동기를 수행할 수 있다.
    increment(context) {
      setTimeout(() => {
        context.commit("increment");
      }, 2000);
    },
    increase(context, payload) {
      setTimeout(() => {
        context.commit("increase", payload);
      }, 2000);
    },
  },
});
```

● App.vue

```vue
<script>
export default {
  methods: {
    addOne() {
      // this.$store.dispatch({type: 'increase', value: 10 }); => payload가 있을 경우
      this.$store.dispatch("increment");
    },
  },
};
</script>
```

### context

- commit, dispatch, getters, state등 많은 메서드를 가지고 있다.
- `dispatch`는 HTTP통신에서 유용한데 요청이 성공하면 성공 액션을 트리거하고 오류가 발생하면 오류처리 액션을 트리거할 수 있다.
- `getters`는 게터에서 얻은 특정값을 가져올 수 있고, `state`로 직접적으로 값을 변경할 수 있다.(🔥 하지만 액션 내부에서는 상태를 조작하면 안된다. 상태를 조작하는 것은 mutations에서만 해야한다.)

## 매퍼 헬퍼(mapper helper)사용하기

### mapGetters

- mapGetters함수는 `연산 객체 내부`에서 사용할 수 있으며, 호출 시 객체를 반환하는데 `스프레드연산자`를 사용해서 `연산 프로퍼티 내부`로 객체를 퍼뜨릴 수 있다.
- mapGetters함수는 `배열`을 인수로 받는데 여기서 `게터 이름을 문자열`로 나열한다.

```vue
<template>
  <!-- <h2>{{ counter }}</h2> -->
  <h2>{{ finalCounter }}</h2>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  computed: {
    // counter() {
    //   // return this.$store.getters.finalCounter;
    // },
    ...mapGetters(["finalCounter"]), // finalCounter가 연산 프로퍼티로 된다.
  },
};
</script>
```

### mapActions

- mapActions함수는 methods 객체 내부에서 사용할 수 있으며, 호출 시 객체를 반환하는데 `스프레드연산자`를 사용해서 `methods 객체 내부`로 객체를 퍼뜨릴 수 있다.
- 인수로 `메서드의 이름을 키로 갖는 객체`를 전달해 메서드의 이름을 변경할 수 있다. => mapGetters도 똑같이 사용할 수 있다!!

```vue
<template>
  <!-- <button @click="addOne">add 1</button> -->
  <button @click="increment">add 1</button>
  <button @click="inc">add 1_1</button>
  <button @click="increase({ value: 10 })">add 10</button>
  <button @click="increase2({ value: 10 })">add 10</button>
</template>

<script>
import { mapActions } from "vuex";

export default {
  methods: {
    // addOne() {
    //   this.$store.dispatch('increment');
    // },
    ...mapActions(["increment", "increase"]),
    ...mapActions({
      inc: "increment",
      increase2: "increase",
    }),
  },
};
</script>
```

## 모듈로 저장소 나누기

- `modules객체` 추가하여 여러 저장소 하나로 합칠수 있다.
- 이때 저장소들은 같은 레벨로 결합된다.
- 이때 상태(state)는 모듈의 지역상태로 변경되고, 변형,액션,게터는 전역상태이다. 모듈 내의 상태는 해당 모듈에만 적용이 된다.

```js
const counterModule = {
  state() {
    return {
      counter: 0,
    };
  },
  mutations: {...},
  actions: {...},
  getters: {...},
};

const store = createStore({
  modules: { // 모듈 결합
    numbers: counterModule,
  },
  state() {
    return {
      isLoggedIn: false,
    };
  },
  mutations: {
    setAuth(state, payload) {
      state.isLoggedIn = payload.isAuth;
    },
  },
  actions: {
    login(context) {
      context.commit('setAuth', { isAuth: true });
    },
    logout(context) {
      context.commit('setAuth', { isAuth: false });
    },
  },
  getters: {
    userIsAuthenticated(state) {
      return state.isLoggedIn;
    },
  },
});
```

### 네임스페이스 모듈

- 네임스페이스를 사용행서 지역상태 외에도 `모듈 전체를 지역 모듈`로 만들 수 있다.
- 모듈 객체에 `namespaced: true`를 추가한다. true로 설정하면 상태뿐만 아니라 모듈 전체(변형,액션,게터)가 저장소로부터 분리된다.

```js
// getters이름 앞에 네임스페이스 이름을 작성하고 /를 붙인다.
computed: {
  counter() {
    return this.$store.getters['numbers/normalizedCounter'];
  },
  // mapGetters는 첫번째 인수로 네임스페이스를 추가한다.
  ...mapGetters('numbers', ['finalCounter']),
},
methods: {
  ...mapActions('numbers', ['increment', 'increase']),
  ...mapActions('numbers', {
    inc: 'increment',
    increase2: 'increase',
  }),
},
// 직접 dispatch할 경우
methods: {
  addOne() {
    // this.$store.dispatch({type: 'numbers/increse', value: 10 });
    this.$store.dispatch('numbers/increment');
  },
},
```
