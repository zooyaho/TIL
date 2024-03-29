# vue를 이용한 DOM상호작용

- vue는 선언형 접근 방식을 사용한다.(= 개발자가 목표만 선언하면 중간 단계는 신경 쓸 필요가 없어진다는 의미)

## 기본 1

1. 글로벌 객체 생성
2. 뷰앱에서 어떤 데이터를 사용할 것인지 설정한다(react의 state와 비슷한 개념인가..?)
3. `data: function(){}` 객체의 메서드를 축약할 수 있음 => `data(){}`, methods 객체형 프로퍼티 추가 -> html에서 등록한 함수를 사용할 수 있다!
4. 연결할 요소에 속성으로 `v-model=""`로 연결, `v-on:click=""`
5. `v-for=""` : 요소를 여러번 복제할 수 있다. wow~! `ex) <li v-for="goal in goals">{{ goal }}</li>`

- app.js

```js
Vue.createApp({
  data() {
    return {
      goals: [],
      enteredValue: "",
    };
  },
  methods: {
    addGoal() {
      this.goals.push(this.enteredValue);
    },
  },
}).mount("#app");
// .mount() : DOM의 요소를 선택해 Vue앱이 페이지 어느 부분을 조정할지 Vue에 알려줘야 한다.
```

- index.html

```html
<body>
  <div id="app">
    <div>
      <label for="goal">Goal</label>
      <input type="text" id="goal" v-model="enteredValue" />
      <button v-on:click="addGoal">Add Goal</button>
    </div>
    <ul>
      <li v-for="goal in goals">{{ goal }}</li>
    </ul>
  </div>
  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
  <script src="app.js"></script>
</body>
```

## 기본 2

1. 보간법: 괄호속({{}})에 data에서 반환하는 객체의 일부분인 프로퍼티를 참조하고 출력되도록하는 방법이다. vue가 제어하는 HTML코드에서 보간법을 사용한다.
2. v-bind : 뷰를 통해 동적으로 ref속성을 통해 값을 출력하는 경우, 뷰 direcive, HTML코드에 추가로 넣는 지시사항이다. 뷰에게 바인딩을 지시한다. => HTMl의 요소 속성값을 설정하도록 지시

- HTML태그 사이에 값을 설정할 때는 보간법을 사용하지만 HTMl태그 위에 즉 속성에 값을 설정할 때는 v-bind를 사용한다.

3. 보간법에서 기본적인 js코드를 사용할 수 있을 뿐더러(복잡한 코드는 안됨), methods에 설정한 함수들도 호출할 수 있다!
4. vue는 data객체에서 반환하는 데이터 전체를 가져다가 데이터를 병합해서 전역 vue 인스턴스 객체(= Vue.createApp)를 만듭니다.
5. v-html : vue에 해당 콘텐츠가 HTML로 인식된다.

- app.js

```js
const app = Vue.createApp({
  data() {
    return {
      courseGoals: ["vue 기본 배우기!"],
      htmlGoal: "<h2>html로 인식해주세요!</h2>",
      vueLink: "https://vuejs.org/",
    };
  },
  methods: {
    outputGoal() {
      const randomNum = Math.random();
      if (randomNum > 0.5) return "vue 더 배워!";
      else if (randomNum === 0.5) return this.courseGoals;
      else return "잘 하구 있구만~~";
    },
  },
});

app.mount("#user-goal");
```

- index.html

```html
...
<section id="user-goal">
  <h2>My Course Goal</h2>
  <p>{{ courseGoals[0] }}</p>
  <p>{{ outputGoal() }}</p>
  <p v-html="htmlGoal"></p>
  <p><a v-bind:href="vueLink">Vue</a>의 공식 문서</p>
</section>
...
```

## 기본3

1. $event : vue에서 제공하는 예약된 이름이며, 브라우저를 통해 자동으로 얻는 '기본 내장 이벤트 객체'에 대한 엑세스를 제공합니다.
2. 이벤트 수식어

- v-on:submit : 브라우저의 기본 기능(폼 제출 시 서버로 요청을 보내는 것, 이때 새로고침이 된다)을 막는 법!
- 방법1) 기본 js에 내장된 event.preventDefault()를 호출한다.
- 방법2) vue에서 제공하는 이벤트 수식어를 사용한다. => v-on:submit.prevent
- @click.left(기본값), @click.right, @click.stop 등 다양한 수식어가 있음.
- 해강 수식어에 맞는 동작을 할 경우 등록된 이벤트가 실행된다. 여기서 stop은 실행되지 않고 중단된다!!

3. v-once : 초기 상태를 유지하면서 상태를 바꾸고 싶지 않을 때 사용할 수 있다. 보간법을 한번만 평가하게된다!!

- app.js

```js
const app = Vue.createApp({
  data() {
    return {
      counter: 0,
      firstName: "",
      lastName: "",
    };
  },
  methods: {
    incerement() {
      this.counter += 1;
    },
    decerement() {
      this.counter -= 1;
    },
    setFirstName(event, defaultValue) {
      this.firstName = defaultValue + " " + event.target.value;
    },
    setLastName(event) {
      this.lastName = event.target.value;
    },
  },
});

app.mount("#events");
```

- index.html

```html
<section id="events">
  <h2>Events in Action</h2>
  <!-- HTML코드는 출력에 집중해야하기 때문에 js파일에서 수행하는것이 좋음! -->
  <button v-on:click="counter++">Add</button>
  <button v-on:click="counter--">Remove</button>
  <button v-on:click="incerement">Add(fn)</button>
  <button v-on:click="decerement">Remove(fn)</button>
  <p>Result: {{ counter }}</p>
  <p v-once>Starting Count: {{ counter }}</p>
  <!-- event중에 input이란 이벤트가 있음!-->
  <input type="text" v-on:input="setFirstName($event, '저의 이름은')" />
  <input type="text" v-on:input="setLastName" />
  <!-- $event : vue에서 제공하는 예약된 이름이며, 브라우저를 통해 자동으로 얻는 기본 내장 이벤트 객체에 대한 엑세스를 제공합니다. -->
  <p>Your name: {{ firstName }} {{ lastName }}</p>
</section>
```

## 기본4

1. v-model : reset input 버튼을 클릭하면 name, input모두 reset된다. 이런 패턴이 많이 사용되어 vue에서는 이 패턴을 단순화한 특수 내장 디렉티브가 있다.

- 이 디렉티브가 관리할 데이터 프러퍼티만 넣어주면 된다!
- `v-on:input, v-bind:vlaue의 축약어이다!`
- 이게 바로 양방향 바인딩이라고 불리는 개념이다!
- 입력 요소에서 나오는 이벤트 즉 입력 이벤트를 수신하는 동시에 value 속성 및 value 프로퍼티를 통해 값을 입력요소에 다시 보낸다. => 데이터 바인딩과 이벤트 처리의 결합이라고 볼 수 있다!

2. vue앱을 마운팅해서 vue 앱 템플릿을 만든다

- 만약 name 출력요소에 보간법으로 함수호출을 작성하였다면 counter변경 버튼 실행 시 counter의 값이 변경되어 vue는 해당 템플릿을 mount하게 된다. 그래서 name에 적용한 함수가 재실행(재평가)이 된다. 성능면에서 좋지 않음 => 이때 연산 프러퍼티를 사용하면 된다!

3. 연산(computed) 프러퍼티 : 메서드와는 다른 중요한 차이점은 vue가 의존성을 인식하고 의존성 중 하나가 변경된 경우에만 재실행하게 된다!!

- 메서드는 `데이터 프러퍼티`처럼 사용하기 때문에 함수명을 명사로 지어야함.
- ⭐️ 사용시 호출형태가 아닌 가리키(pointer)는 형태로 작성해야한다. vue가 호출을 함!!
- 성능면에서 값을 출력하는 대부분의 경우 메서드보다 연산 프로퍼티를 사용하는 것이 좋다.
- 이벤트에 따른 로직은 메서드를 사용 => 변하지 않기 때문

4. 감시자(watcher) : 기본적으로 의존성 중 하나가 변경될 때 Vue에 실행할 수 있도록 지시할 수 있는 함수이다.

- 실제 연산 프로퍼티 대신 사용할 수 있으며, 객체를 취한다.
- 데이터 프로퍼티와 연산 프로퍼티에 사용한 이름을 사용할 수 있다.
- 데이터 프로퍼티에 있는 이름을 사용하여 메서드를 사용할 경우, 값이 변경되면 watch메서드의 로직이 실행한다.
- 인자로 설정한 데이터 프로퍼티의 마지막 값이 전달된다!
- 특정 상황일 때 값이 변경해야하는 경우 사용이 적합하다. 예를들어 특정 데이터가 변경되어 보내는 HTTP요청이나 특정값이 변경되면 설정하는 타이머가 있다.

```js
watch(newValue, oldValue){...}
```

🔥결론
=> 이벤트 바인딩에는 메서드(methods)를, 다른 데이터의 의존하는 데이터 출력엔 연산(computed) 프러퍼티, HTTP나 타이머 설정, 로컬스토리지에 데이터를 저장하는 작업은 감시자(watch) 프로퍼티를 사용한다!!

5. 축약어

- @ === v-on ex) v-on:click="..." === @click="..."
- : === v-bind ex) v-bind:value="..." === :value="..."

- app.js

```js
const app = Vue.createApp({
  data() {
    return {
      counter: 0,
      name: "",
      fullName2: "",
    };
  },
  watch: {
    name(value) {
      // ⭐️ name값이 변경 될 경우 이 함수는 재실행 됨.
      console.log("watch 메서드가 재실행 됩니다!");
      if (this.name === "") this.fullName2 = "";
      else this.fullName2 = value + " " + "watch default value";
    },
    counter(value) {
      // counter와 같은 기능에 더 적합함
      if (value > 5) this.counter = 0;
      if (value > 3)
        setTimeout(() => {
          this.counter = 0;
        }, 2000);
    },
  },
  computed: {
    fullName() {
      // 데이터 프러퍼티처럼 사용하기 때문에 함수명을 명사로 지어야함.
      console.log("computed 메서드가 재실행 됩니다!");
      if (this.name === "") return "";
      else return this.name + " " + "computed default value";
    },
  },
  methods: {
    incerement() {
      this.counter += 1;
    },
    decerement() {
      this.counter -= 1;
    },
    setName(event, defaultValue) {
      this.name = defaultValue + " " + event.target.value;
    },
    resetName() {
      this.name = "";
    },
    outputFullName() {
      console.log("재실행 됩니다!"); // count가 변경 될 때도 해당 함수가 재실행 됨!
      if (this.name === "") return "";
      else return this.name + "default value";
    },
  },
});

app.mount("#events");
```

- index.html

```html
<section id="events">
  <h2>Events in Action</h2>
  <button v-on:click="incerement">Add(fn)</button>
  <button v-on:click="decerement">Remove(fn)</button>
  <p>Result: {{ counter }}</p>
  <p v-once>Starting Count: {{ counter }}</p>
  <!-- reset input 버튼을 클릭하면 name, input모두 reset된다.-->
  <!-- <input
        type="text"
        v-bind:value="name"
        v-on:input="setName($event, '저의 이름은')"
      /> -->
  <input type="text" v-model="name" />
  <button v-on:click="resetName">Reset input</button>
  <!-- <p>{{ outputFullName() }}</p> -->
  <p>{{ fullName }}</p>
  <p>{{ fullName2 }}</p>
  <p>Your name: {{ name + ' ' + 'default value'}}</p>
</section>
```

## 기본5 - 동적 스타일링

### 인라인 스타일을 동적으로 사용

- `v-bind:style="{}"`을 사용하여 동적스타일링을 할 수 있다.
- 인라인 스타일 적용은 코드의 간결성이 떨어지기 때문에 사용을 자제하는 것이 좋음.

### class에 동적으로 스타일링

- `v-bind:class=""`
- `v-bind:class={}` : 가독성에 좋다, 배열로 클래스를 여러개 설정할 수 있다.
- ⭐️ 하드코딩된 class속성과 동적 클래스 바인딩을 같이 사용할 수 있다, vue가 동적 클래스 바인딩 결과를 먼저 평가한 다음에 하드코딩된 클래스와 병합하게 된다.

- app.js

```js
const app = Vue.createApp({
  data() {
    return {
      boxASelexted: false,
      boxBSelexted: false,
      boxCSelexted: false,
      boxDSelexted: false,
    };
  },
  methods: {
    boxSelected(box) {
      if (box === "A") this.boxASelexted = true;
      else if (box === "B") this.boxBSelexted = true;
      else if (box === "C") this.boxCSelexted = true;
      else if (box === "D") this.boxDSelexted = true;
    },
  },
});

app.mount("#styling");
```

- index.html

```html
<section id="styling">
  <div
    class="demo"
    :style="{borderColor: boxASelected ? 'red' : '#ccc'}"
    @click="boxSelected('A')"
  ></div>
  <div
    :class="boxBSelected ? 'demo active' : 'demo'"
    @click="boxSelected('B')"
  ></div>
  <div
    :class="{demo: true, active: boxCSeleted}"
    @click="boxSelected('C')"
  ></div>
  <!-- 동적 클래스 바인딩을 같이 사용할 수 있다, vue가 동적 클래스 바인딩 결과를 먼저 평가한 다음에 하드코딩된 클래스와 병합하게 된다. -->
  <div
    class="demo"
    :class="{active: boxDSeleted}"
    @click="boxSelected('D')"
  ></div>
  <div :class="['demo',{active: boxESeleted}]" @click="boxSelected('E')"></div>
</section>
```

## 기본6 - 조건부 렌더링

- `v-if, v-else, v-else-if` 디렉티브로 js와 비슷하게 if문을 사용할 수 있다.
- v-else를 사용하려면 요소들이 이웃해야한다.
- ⭐️ v-if대신 `v-show`를 사용할 수 있지만 v-else, v-else-if를 같이 사용할 수 없다. v-if와의 차이점으로는 조건부렌더링을 할때, v-if는 dom에서 완전 제거하고, v-show는 dom에 제거하지않고 display:none으로 변경되어 보이지 않게 된다. v-show는 성능에 영향을 주지 않는다. 하지만 평소에는 v-if를 사용하는 것이 좋고, 가시성 상태가 자주 바뀌는 요소(토글)가 있을 경우 v-show를 사용하는 것이 좋다.
- `v-for="item in arr"`로 데이터 프로퍼티를 출력할 수 있다. index 출력을 위해 `(item, idx) in arr`로 작성하면 된다. 객체 또한 출력이 가능한데 value만 출력된다. `(value, key, index) in obj`로 key 또한 출력 가능하다.
- `v-for="num in 3"`으로 하면 1-3까지 출력이 가능하다.

```html
<li v-for="(item, idx) in arr">{{ item }} - {{ idx }}</li>
<li v-for="(value, key) in {name: 'Mark', age: 25}">{{ value }} - {{ key }}</li>
// Mark - name \n 25 - age 출력
```

- v-for를 사용할 때 `:key=""`를 추가해야 하는데, `key`는 출력하는 모든 항목에 대해 고유 식별 기준으로 작용하게 한다. key를 사용해야 dom을 재사용하는 vue가 각각의 요소들을 식별하여 업데이트를 수행할 수 있다.
