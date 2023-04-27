# animation

## ● trasition 컴포넌트

- 전환 컴포넌트는 하나의 요소, 즉 하나의 직속 자식만을 갖는다.
- 래핑된 요소가 mounted되기 전 래핑된 요소에 \*-enter-from, \*-enter-active, \*-enter-to와 같은 특수 css클래스를 추가한다.
- \*-enter-from, \*-enter-active가 추가되고 \*-enter-from 삭제 \*-enter-to추가 방식으로 이루어 진다.

```txt
*-enter-from(추가), *-enter-active(추가) => *-enter-from(삭제), *-enter-active(생존) => *-enter-active(생존), *-enter-to(추가)
```

- v-if를 통해 조건이 거짓이라고 판단되면 먼저 마운트된 요소가 마운트 해제된다.

```txt
*-leave-from(추가), *-leave-active(추가) => *-leave-from(삭제), *-leave-active(생존) => *-leave-active(생존), *-leave-to(추가)
```

- 해당 animation 시간이 종료돼야 **DOM에서 요소를 제거**한다.

### name 속성

> name속성을 추가하여 특수 클래스를 지정하여 여러개 사용할 수 있다.(사용자 지정 접두사 추가하여 정의)

● \<template>

```html
<div class="container">
  <transition name="para">
    <p v-if="paraIsVisible">문단입니다!</p>
  </transition>
  <button @click="toggleParagraph">Toggle paragraph</button>
</div>
```

● \<style>

```css
.para-enter-from {
  opacity: 0;
  transform: translateY(-30px);
}
.para-enter-active {
  transition: all 0.3s ease-out;
  /* animation: slide-scale 0.3s ease-in; */
}
.para-enter-to {
  opacity: 1;
  transform: translateY(0);
}
.para-leave-from {
  opacity: 1;
  transform: translateY(0);
}
.para-leave-active {
  transition: all 0.3s ease-in;
  /* animation: slide-scale 0.3s ease-out; */
}
.para-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
```

### 사용자 지정 속성 추가

> 서드파티 CSS 애니메이면 라이브러리 사용 등, 완전히 다른 사용자 지정 CSS클래스 이름을 사용하고자 할 경우

> 🔥 필요로 하는 CSS클래스 이름을 임의로 지정할 수 있다.
> ⭐️ transition컴포넌트는 하나의 자식 요소만 허락하기 때문에 루트요소가 두개일 때는 적용되지 않는다.(직속자식요소 하나만 있어야 animation이 작동된다.)

```html
<template enter-to-class="some-scale" enter-active-class="..."> ... </template>
```

### 하나 이상의 직속 자식요소가 가능한 경우(두 버튼)

- v-if, v-else를 통해 Vue에게 전환되는 요소를 명확하게 알려주면 된다.
- ⭐️mode="" 속성: 'out-in', 'in-out'이 있으며, 'out-in'는 사라지는 전환 뒤 활성화 되는 animation으로 진행된다. 'in-out'는 반대이다.

```html
<div class="container">
  <transition name="fade-button" mode="out-in">
    <button @click="showUsers" v-if="!usersAreVisible">Show Users!</button>
    <button @click="hideUsers" v-else>Hide Users!</button>
  </transition>
</div>
```

```js
// script
data() {
    return {
      // ...
      usersAreVisible: false,
    };
},
methods: {
  showUsers() {
    this.usersAreVisible = true;
  },
  hideUsers() {
    this.usersAreVisible = false;
  },
}
```

```css
/* style */
.fade-button-enter-from,
.fade-button-leave-to {
  opacity: 0;
}
.fade-button-enter-active {
  transition: opacity 0.3s ease-out;
}
.fade-button-leave-active {
  transition: opacity 0.3s ease-in;
}
.fade-button-enter-to,
.fade-button-leave-from {
  opacity: 1;
}
```

### transition의 내장 메서드!

- @beforeEnter="event Fn": v-enter-from 실행 전 해당 메서드가 실행된다.
- @enter="event Fn": v-enter-active 실행 전 해당 메서드가 실행된다.
- @after-enter="event Fn": enter에 등록한 메서드가 끝나고 실행된다.(애니메이션이 끝나고 실행된다.)

- @before-leave="event Fn": v-leave-from 실행 전 해당 메서드가 실행된다. 래핑된 요소가 DOM을 빠져나갈때나 해당하는 이벤트가 발생할 시 실행된다.
- @leave="event Fn": v-leave-active 실행 전 해당 메서드가 실행된다.
- @after-leave="event Fn": leave 등록한 메서드가 끝나고 실행된다.(애니메이션이 끝나고 실행된다.)

- 👉🏻 등록한 해당 이벤트 메서드에는 인수로 요소(전환되는 요소)와 done메서드를 갖는다.

- @enterCancelled, @leaveCancelled 두 이벤트는 이벤트가 삭제되면 항상 발생한다. ⭐️ 기존 전환 효과가 끝나지 않았는데 새로운 전환효과가 시작하려고 하면 경우에 맞는 cancelled 이벤트가 발생한다.

```html
<div class="container">
  <transition
    name="para"
    @before-enter="beforeEnter"
    @enter="enter"
    @after-enter="afterEnter"
    @before-leave="beforeLeave"
    @leave="leave"
    @after-leave="afterLeave"
    @enter-cancelled="enterCancelled"
    @leave-cancelled="leaveCancelled"
  >
    <p v-if="paraIsVisible">문단입니다!</p>
  </transition>
  <button @click="toggleParagraph">Toggle paragraph</button>
</div>
```

```js
// script
data() {
    return {
      // ...
      enterInterval: null,
      leaveInterval: null,
    };
},
methods: {
  enterCancelled() {
    console.log('enter cancelled 실행');
    clearInterval(this.enterInterval);
  },
  leaveCancelled() {
    console.log('leave cancelled 실행');
    clearInterval(this.leaveInterval);
  },
  beforeEnter(el) {
    console.log('before enter 실행중', el);
    el.style.opacity = 0;
  },
  enter(el, done) {
    console.log('enter 실행중');
    let round = 1;
    this.enterInterval = setInterval(() => {
      el.style.opacity = round * 0.01;
      round++;
      if (round > 100) {
        clearInterval(this.enterInterval);
        // 자체 로직으로 css를 변경할때는 Vue에게 명확하게 동작 끝을 알려야한다.
        done();
      }
    }, 20);
  },
  afterEnter() {
    console.log('after enter 실행중!');
  },
  beforeLeave() {
    console.log('before leave 실행중');
  },
  leave(el, done) {
    console.log('leave 실행 중');
    let round = 1;
    this.leaveInterval = setInterval(() => {
      el.style.opacity = 1 - round * 0.01;
      if (round > 100) {
        clearInterval(this.leaveInterval);
        done();
      }
      round++;
    }, 20);
  },
  afterLeave() {
    console.log('after leave 실행 중');
  },
}
```

### :css="boolean" 속성

- :css="false" : css를 사용하지 않는다고 Vue에게 알려준다, transition컴포넌트를 오로지 js가 제어한다는 뜻
- 작성하는 이유는 Vue에게 각 CSS 클래스에서 CSS코드를 찾는 수고로움을 덜어줄 수 있다. => CSS분석 단계를 건너뛸 수 있다!(불필요한 단계를 건너뛰어 성능면에서 좋다.)

## transition-group컴포넌트

- 여러 요소에서도 사용할 수 있다. 또한 여러 요소에 동시에 애니메이션을 적용할 수 있다.
- 여러 항목을 동시에 추가하거나 삭제 시에도 애니메이션이 동일하게 적용되기 때문에 유용하게 사용할 수 있다.
- transition컴포넌트와 다르게 DOM에 렌더링 된다.
- 👉🏻 tag속성을 통해 렌더링할 요소를 지정한다.
- 삭제되고 추가되지 않는 다른 요소들의 애니메이션을 적용할 수 있다. => \*-move {}를 사용한다. (Vue는 삭제되고 추가되지 않는 다른 요소를 새로운 위치로 이동시킬 때 내부적으로 transform을 사용한다!!)

```html
<template>
  <transition-group tag="ul" name="user-list">
    <li v-for="user in users" :key="user" @click="removeUser(user)">
      {{ user }}
    </li>
  </transition-group>
  <div>
    <input type="text" ref="addUser" />
    <button @click="addUserHandler">Add User</button>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        users: ["daisy", "max", "mark"],
      };
    },
    methods: {
      removeUser(removeUser) {
        this.users = this.users.filter((user) => user !== removeUser);
      },
      addUserHandler() {
        this.users.unshift(this.$refs.addUser.value);
        this.$refs.addUser.value = "";
      },
    },
  };
</script>

<style scoped>
  ul {
    list-style-type: none;
    margin: 1rem 0;
    padding: 0;
  }
  li {
    border: 1px solid #ccc;
    padding: 1rem;
    text-align: center;
  }

  .user-list-enter-from {
    opacity: 0;
    transform: translateX(-30px);
  }
  .user-list-enter-active {
    transition: all 1s ease-out;
  }
  .user-list-enter-to,
  .user-list-leave-from {
    opacity: 1;
    transform: translateX(0px);
  }
  .user-list-leave-active {
    transition: all 1s ease-in;
    /* 삭제되는 요소의 위치를 지정 */
    position: absolute;
  }
  .user-list-leave-to {
    opacity: 0;
    transform: translateX(30px);
  }
  .user-list-move {
    transition: transform 0.8s ease;
  }
</style>
```

### 라우트 변경에 애니메이션 적용하기

- 🔥 라우트 변경 시 초기 애니메이션이 적용되는데 이를 방지하려면 `router.isReady()`를 적용하면 된다.
- `router.isReady()`는 프로미스를 반환하는데 콜백함수에서 app을 mount시키면 된다.

```js
router.isReady().then(() => {
  app.mount("#app");
});
```

● vue3

```html
<template>
  <router-view v-slot="{ Component }">
    <transition name="route" mode="out-in">
      <component :is="Component"></component>
    </transition>
  </router-view>
</template>
```

● vue2

```html
<template>
  <transition name="route" mode="out-in">
    <router-view></router-view>
  </transition>
</template>
```
