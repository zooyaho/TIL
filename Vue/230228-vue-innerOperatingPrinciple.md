# vue의 내부동작 원리

## 뷰의 내장된 반응성

- vue는 data 객체의 모든 프로퍼티를 전역 프로퍼티와 합쳐서 내부에서 관리하는 하나의 객체로 만듭니다.이때 메서드도 해당 객체에 합쳐진다.
- data객체의 핵심적인 것은 vue가 data 객체의 프로퍼티를 js의 기능인 프록시로 래핑해서 반응형 데이터 객체로 바꾼다.

## proxy

- javascript에 내장되어있다.

```js
let message = "hello!";
let longMsg = message + "ok!";
console.log(longMsg); // hello!ok!
message = "hi!";
console.log(longMsg); // hello!ok! => 변경되지 않음..ㅜㅜ
// js는 반응형이 아니기 때문에 앞서 작성한 연산이 실행되지 않음.
```

- proxy 사용하여 자체적인 반응형 시스템 구축하기

```js
const data = {
  message: "hello",
  longMessage: "hello world",
};
const handler = {
  // 프록시가 지원하는 일명 트랩을 설정할 수 있다.
  set(target, key, value) {
    // set() 자동으로 인수를 받음. 이 setter함수는 proxy에 새로운 프로퍼티가 설정될 때마다 트리거된다.
    // target: 래핑된 값, key: 프로퍼티 이름, value: 프로퍼티 값
    console.log(target); // {message: 'hello'} => 원본값
    console.log(key); // message
    console.log(value); // Hello => 원본 값이 변경되지는 않음.

    if (key === "message") {
      target.longMessage = value + " world!";
    }
    target.message = value; // 원본값 변경
  },
};
const proxy = new proxy(data, handler);
// data가 proxy에 래핑되기 때문에 data의 massage를 엑세스할 수 있다.
prosy.message = "Hello"; // 새로운 프로퍼티 설정으로 setter함수 트리거 됨!
console.log(proxy.longMessage); // "Hello world"
```

👉🏻 이러한 반응형 작업을 Vue가 내부적으로 한다. 모든 data프로퍼티를 추적하고 프로퍼티에 변경 사항이 발생하면 앱에서 해당 프로퍼티가 사용된 부분을 업데이트한다❗️❗️

## 템플릿(template)

- HTML코드에서 동일한 부분을 여러 앱으로 제어하면 안됨. 또한 하나의 앱으로 HTML코드의 여러부분을 제어하는 것도 안된다.
- 제어되는 HTML부분을 `template`라고 한다.
- ⭐️ vue앱을 HTML코드의 DOM 내 특정 위치에 마운트할 때면, 해당 HTML 코드 부분을 Vue앱의 템플릿으로 지정하게 된다.

### 템플릿을 추가하는 또 다른 방법

- 앱구성 객체에 template옵션을 사용한다. 값을 문자열이다.
- 자식요소들이 값으로 할당된다!

```js
const app2 = Vue.createApp({
  template: `
    <p>{{ favMeal }}</p>
  `,
  data() {
    return {
      favMeal: "meal",
    };
  },
});
app2.mount("#app2");
```

## Ref

- input, p, section, h, textarea 등 필요할 때만 값을 가져오기 위해 사용
- vue는 ref를 감지하여 내부에 이를 저장한다.
- vue가 지원하는 프로퍼티는 $를 붙여서 사용하는데, js에서 ref를 사용하기 위해 `$refs`를 사용한다. `$refs`는 키-값쌍으로 이루어져 있어 템플릿에 지정한 ref값을 사용하여 요소의 값(요소자체)을 가져온다.

- index.html

```html
<section id="app">
  <h2>How Vue Works</h2>
  <input type="text" ref="userText" />
  <button @click="setText">Set Text</button>
  <p>{{ message }}</p>
</section>
```

- app.js

```js
const app = Vue.createApp({
  data() {
    return {
      message: "Vue is great!",
    };
  },
  methods: {
    setText() {
      this.message = this.currentUserInput;
      this.message = this.$refs.userText.value;
      console.log(this.$refs.userText); // <input type="text" />
      console.dir(this.$refs.userText); // dir => 해당 요소객체의 프로퍼티를 확인할 수 있다.
    },
  },
});
```

## Vue가 DOM을 업데이트하는 방법

- 성능최적화 방식으로 업데이트를 수행하는 일
- vue앱, vue 인스턴스는 데이터와 연산 프로퍼티, 메서드 등을 저장한다.
- 브라우저 DOM은 Vue가 제어하는 템플릿 DOM에 렌더링한다.
- 뷰가 제어하는 렌더링 스포세스 중에 동적 부분과 플레이스홀더 보간, 그리고 바인딩이 제거되며 실제 값이 삽입된다.
- 뷰에 대한 지시사항(이벤트 바인딩, 보간을 통한 데이터 출력)은 템플릿이 DOM에 렌더링되고 나면 뷰에 의해 삭제되기 때문에 DOM에서 이 지시사항을 확인할 수 없다.
- 뷰가 변경된 값을 감지하여 해당 일부만을 업데이트할 수 있는 것은 이전 DOM값과 새로운 DOM값을 비교하여 차이가 있는 것만 업데이트하기 때문이다. 하지만 실제로 이런식이 아니다?!?! => 변경사항이 발생할 때마다 브라우저가 렌더링한 실제 DOM을 모두 읽어들이는 작업 또한 성능 저하 요인이기 때문이다.
- 가상DOM이란 실제 DOM의 가상 복사본이며, 전적으로 js가 관리하므로 JS기반 DOM이고 메모리에 위치합니다.
- 가상DOM은 JS객체의 집합으로 Vue는 여기서 어떤 HTML태그가 필요하며 해당 태그의 콘텐츠가 무엇인지를 기억한다. 그리고 데이터에 업데이트가 발생하면 새로운 가상 DOM을 생성해서 새로운 가상 DOM과 기존 가상 DOM을 비교한다. 그리고 두 가상 DOM 간 차이점만 실제 DOM에 쓰인다❗️❗️ => 실제로 상시 가상 DOM전체를 재생성하지 않기 때문에 성능을 상당히 개선해준다!!

## 뷰 인스턴스 라이프사이클

- 앱을 생성하고 화면에 무언가 나타나기 시작하면 해당앱이 몇 개의 지점에 도달한다. 이 지점은 생명주기 단계이다.
- HTML코드, 즉 DOM에 mount메서드로 마운트해준다.
- createApp({..}) => beforeCreate() => created() => beforeMount() => mounted() =>
- `beforeCreate()` : 앱이 완전 초기화되기 이전에 호출
- `created()` : 초기화가 완료되고 아직 마운트가 되기 전 상태, 이 시점까지는 화면에 표시되는 것이 아무것도 없다.
- created단계가 지나서야 Vue가 데이터 프로퍼티를 인식하고 일반 앱 구성에 대해서도 인지하는데 이제부터 템플릿을 컴파일한다. => 모든 동적 플레이스홀더와 보간등이 제거된 후 사용자에게 표시될 구체적인 값으로 대체된다.
- `beforeMount()` : vue가 화면에 무언가 표시하기 직전(마운트 되기 직전) 단계로 화면을 통해 볼 수 있게 되기 바로 직전 단계입니다.
- `mounted()` : 마운트된 이후 단계로 화면에 뭔가 나타난다. 이제 vue앱이 초기화되고 템플릿도 컴파일을 마쳤기 때문에 화면에 표시되는 출력값이 생긴다. vue가 화면에 표시할 대상을 인지하고 브라우저에 지시 사항을 넘겨서 브라우저가 이에 따라 vue 앱이 정의한 대로 모든 콘텐츠가 있는 HTML요소를 추가한다. 이것으로 마운트된 vue앱(인스턴스)가 완성된다.
- 대부분의 뷰앱에서는 데이터에 대한 변경 사항이 발생한다. 그럴때면 새로운 생명 주기가 트리거된다. beforeUpdate() => updated()훅으로 이어진다.
- `beforeUpdate()` : 뷰 앱내에서 업데이트를 완전히 처리하지 않았을 때에 대한 단계
- `updated()` : 해당 처리(업데이트)가 완료되었을 때에 대한 단계, 다시 mounted단계를 거칠 필요가 없다.
- 종종 뷰앱이 마운팅 해제되는 경우가 있는데 앱 마운트가 해재되면 화면의 모든 콘텐츠가 삭제되며 앱 사용이 불가해진다.
- `beforeUnmount()` : 콘텐츠(뷰앱) 삭제 직전에 실행되는 단계, 콘텐츠(뷰앱)가 삭제 되기 전 상태
- `unmounted()` : 콘텐츠(뷰앱) 삭제 후 실행되는 단계, 콘텐츠(뷰앱)가 삭제 되고 난 뒤 상태
