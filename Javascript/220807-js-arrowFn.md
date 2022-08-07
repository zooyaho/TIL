220807 - TIL

> 참고 모던 자바스크립트 Deep Dive

# 화살표 함수

- 콜백 함수 내부에서 this가 전역객체를 가리키는 문제를 해결하기 위한 대안으로 유용하다.
- 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor이다.
- 중복된 매개변수 이름을 선언할 수 없다.
- 화살표 함수는 함수 자체의 this, arguments, super, new.target 바인딩을 갖지 않는다.

## 1️⃣ this

- this바인딩은 함수의 호출 방식, 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다.
- **일반함수로서 호출되는 모든 함수 내부의 this는 전역객체를 가리킨다.**
- **콜백함수에도 strict mode가 적용되어 일반 함수로서 호출된 모든 함수 내부의 this에는 전역 객체가 아닌 undefined가 바인딩된다!!**  
  🔥 화살표 함수는 함수 자체의 this바인딩을 가지고 있지 않는다. 따라서 화살표 함수 내부에서 this를 참조하면 상위 스코프의 this를 그대로 참조한다.  
  👉🏻 스코프 체인상에서 가장 가까운 상위 함수 중에서 화살표 함수가 아닌 함수의 this를 참조한다.
- 화살표 함수를 제외한 모든 함수에는 this 바인딩이 반드시 존재한다.

👾 콜백함수를 화살표 함수로 사용

```js
function Person(name) {
  this.name = name;
  this.sayHi = function () {
    setTimeout(() => {
      console.log(`${this.name} Hi~!`);
    }, 1000);
  };
}
const me = new Person("Lee");
me.sayHi(); // Lee Hi~!
```

👉🏻 this가 상위 스코프에 바인딩되어 Person을 가리키므로 Person.name = 'Lee'

👾 콜백함수를 일반 함수로 사용

```js
function Person(name) {
  this.name = name;
  this.sayHi = function () {
    setTimeout(function () {
      console.log(`${this.name} Hi~!`);
    }, 1000);
  };
}
const me = new Person("Lee");
me.sayHi(); // Hi~!
```

👉🏻 this가 전역객체를 가리키므로 window.name = ''

## 2️⃣ Rest 파라미터

- 화살표 함수에서는 arguments 객체를 사용할 수 없다.
- 상위 스코프의 arguments 객체를 참조할 수 있지만 화살표 함수 자신에게 전달된 인수 목록을 확인할 수 없다
- 화살표 함수로 가변 인자 함수를 구현해야 할 때는 Rest파라미터를 사용해야 한다.
- **함수에 전달된 인수들의 목록을 배열로 전달 받는다!**
- 일반 매개변수와 같이 사용할 경우 순차적으로 할당된다!
- Rest파라미터는 단 하나만 선언할 수 있다.
- 기본값을 지정할 수 없다.
- 선언한 매개변수 개수를 나타내는 함수 객체의 length 프로퍼티에 영향을 주지 않는다.

```js
function foo(param, ...rest) {
  console.log(param); // 1
  console.log(rest); // [2,3,4]
}
foo(1, 2, 3, 4);
console.log(foo.length); // 1
```
