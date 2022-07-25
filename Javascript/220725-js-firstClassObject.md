220725 - TIL

> 참고 모던 자바스크립트 Deep Dive

# 함수와 일급객체

> 함수를 객체와 동일하게 사용할 수 있다는 의미이며 런타임에 함수 객체로 평가된다.

✍️ 조건

- 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
- 변수나 자료구조(객체, 배열)에 저장할 수 있다.
- 함수의 매개변수에 전달할 수 있다.
- 함수의 반환값으로 사용할 수 있다.

## ● 함수 객체의 프로퍼티

> - 함수 객체는 일반객체에는 없는 함수 고유의 프로퍼티를 소유한다.
> - `Object.getOwnPropertyDescriptors()`메서드로 함수의 모든 프로퍼티의 `프로퍼티 어트리뷰트`를 확인할 수 있다.
> - 상속 받은 \_\_proto\_\_는 `Object.getOwnPropertyDescriptors()`메서드로 확인하면 undefined가 반환된다.!!

```js
function square(num) {
  return num * num;
}

console.log(Object.getOwnPropertyDescriptors(square));
```

![](https://velog.velcdn.com/images/zooyaho/post/8160ca4b-1638-46c4-82a9-613147607a1f/image.png)

### 1. arguments 프로퍼티

> - arguments 프로퍼티의 값은 `arguments 객체`다.
> - 함수 호출 시 전달된 인수들의 정보를 담고 있는 순회 가능한 `유사 배열 객체`이다.
> - 함수 내부에서 지역 변수처럼 사용된다.
> - 초과된 인수는 암묵적으로 arguments객체의 프로퍼티로 보관된다.
> - arguments객체는 매개변수의 개수를 확정할 수 없는 **가변 인자 함수**를 수현할 때 유용하다.

![](https://velog.velcdn.com/images/zooyaho/post/e9458824-c8d5-4d25-acc6-61612c26f3e3/image.png)

#### 📎 callee 프로퍼티

- arguments객체의 callee 프로퍼티는 함수 자신을 가리킨다.

#### 📎 length 프로퍼티

- arguments객체의 length 프로퍼티는 인수의 개수를 가리킨다.

#### 📎 Symbol(Symbol.iterator) 프로퍼티

- arguments객체를 순회 가능한 자료구조인 이터러블로 만들기 위한 프로퍼티이다. Symbol.iterator를 **프로퍼티 키로 사용한 메서드를 구현**하는 것에 의해 이터러블이 된다.

![](https://velog.velcdn.com/images/zooyaho/post/d89d60ba-72a1-4fda-b633-0b20a500b8b2/image.png)

#### arguments객체에 배열 메서드 사용

- 유사 배열 객체는 배열이 아니므로 배열 메서드를 사용할 수 없지만 Function.prototype.call, Function.prototype.apply를 사용해 간접 호출 후 사용할 수 있다.

👾#01

```js
function sum() {
  const array = Array.prototype.slice.call(arguments);
  return array.reduce(function (pre, cur) {
    return pre + cur;
  }, 0);
}

console.log(sum(1, 3)); // 4
```

👾#02 - Rest파라미터 사용

```js
function sum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0);
}

console.log(sum(1, 3, 4)); // 8
```

### 2. length 프로퍼티

> - arguments 객체의 lenth프로퍼티는 **인자의 개수**를 가리키고, 함수 객체의 length프로퍼티는 **매개변수의 개수**를 가리킨다!!!

```js
function bar(x) {
  console.log(bar.length); // 1
  console.log(arguments.length); // 3
  return x;
}
bar(1, 2, 3);
```

### 3. name 프로퍼티

> - 함수 이름을 나타낸다.

```js
const namedFn = function foo() {};
console.log(namedFn.name); // foo

const anonymousFn = function () {};
console.log(anonymousFn.name); // anonymousFn

function bar() {}
console.log(bar.name); // bar
```

### 🚨 참고

<pre>
🔥 함수를 일반 함수로서 호출하면 함수 객체의 내부 메서드[[Call]]이 호출된다.  
🔥 new 연산자로 호출한 생성자 함수는 내부 메서드인[[Construct]]를 호출한다.  
🔥 [[Construct]]를 가지고 있는 함수 객체를 constructor로, [[Construct]]를 가지고 있는 않는 함수 객체를 non-constructor로 구분한다.  
🔥 <strong>일반함수(function(){}), 함수 선언문, 함수 표현식으로 정의된 함수</strong>만이 `constructor`이고 <strong>화살표 함수와 메서드 축약표현으로 정의된 함수</strong>는 `non-constructor`이다.
</pre>

### 4. prototype 프로퍼티

> - prototype 프로퍼티은 생성자 함수로 호출할 수 있는 함수 객체. 즉 constructor만이 소유할 수 있는 프로퍼티이다.
> - 생성자 함수가 생성할 인스턴스의 **프로토타입 객체를 가리킨다.**

```js
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}.hasOwnProperty("prototype")); // true

// 일반 객체는 prototype 프로퍼티가 없다.
({}.hasOwnProperty("prototype")); // false
```

👉🏻 `hasOwnProperty()`메서드는 인수로 전달받은 프로퍼티 키가 객체 고유의 프로퍼티 키인 경우에만 true를 반환하고 상속받은 프로토타입의 프로퍼티인 경우 false를 반환한다.

### 5. \_\_proto\_\_ 접근자 프로퍼티

> - \_\_proto\_\_는 Object.prototype 객체의 접근자 프로퍼티이다.(getter/setter 함수)
> - [[prototype]] 내부 슬롯에 직접 접근할 수 없으며, \_\_proto\_\_ 접근자 프로퍼티를 통해 간접적으로 프로토타입 객체에 접근할 수 있다.

![](https://velog.velcdn.com/images/zooyaho/post/afc0370c-6a8c-4bc4-994d-64d276431754/image.png)

👉🏻 \_\_proto\_\_는 square 함수의 프로퍼티가 아닌, Object.prototype 객체의 프로퍼티를 상속받은 프로퍼티이다.  
👉🏻 Object.prototype 객체의 \_\_proto\_\_ 접근자 프로퍼티는 모든 객체가 사용할 수 있다.

### 🚨 생성자 함수인 함수 객체

<pre>
👉🏻 [[prototype]] 내부 슬롯은 직접 접근할 수 없으므로 <br>Object.prototype 객체의 __proto__ 접근자 프로퍼티를 사용하여 접근해야 한다.
👉🏻 new키워드로 호출한 생성자 함수만이 [[prototype]] 내부 슬롯을 갖는다.
👉🏻 __proto__ 접근자 프로퍼티는 Object.prototype 객체의 프로퍼티다.
👉🏻 모든 객체는 Object.prototype 객체를 상속받아 __proto__ 접근자 프로퍼티를 사용할 수 있지만,<br> 일반 객체는 [[prototype]] 내부 슬롯을 가지고 있지 않아 __proto__ 접근자 프로퍼티가 필요 없다!<br>하지만 [[prototype]] 내부 슬롯을 가지고 있는 생성자 함수(함수 객체)는 __proto__를 사용하여<br>[[prototype]] 내부 슬롯에 간접 접근한다.
</pre>
