220713 - TIL

> 참고) 모던 자바스크립트 Deep Dive

# 객체 리터럴

## 1. 객체 생성 방법

- 객체 리터럴
- Object 생성자 함수
- 생성자 함수
- Object.create 메서드
- 클래스(ES6)

## 2. 프로퍼티 키 동적 생성

```js
let obj = {};
let key = "hello";

// ES5 : 프로퍼티 키 동적 생성
obj[key] = "wold";
// ES6 : 계산된 프로퍼티 이름
let obj2 = { [key]: "world" };

console.log(obj); // {hello: 'world'}
```

## 3. 프로퍼티 접근

- 마침표 프로퍼티 접근 연산자(.)을 사용하는 마침표 표기법
- 대괄호를 프로퍼티 접근 연산자([..])을 사용하는 대괄호 표기법

```js
let person = {
  name: "zooyaho",
};

console.log(person.name); // 마침표 표기법
console.log(person["name"]); // 대괄호 표기법
```

## 4. 프로퍼티 동적 생성

> 존재하지 않는 프로퍼티에 값을 할당하면 프로퍼티가 동적으로 생성되어 추가되고 프로퍼티 값이 할당 된다.

```js
let person = {
  name: "zooyaho",
};

person.age = 24;
console.log(person); // {name: 'zooyaho', age: 24}
```

## 5. 프로퍼티 삭제(delete 연산자)

- delete 연산자는 객체의 프로퍼티를 삭제합니다.
- 이때 delete 연산자의 피연산자는 프로퍼티 값에 접근할 수 있는 표현식이어야 합니다.
- 만약 존재하지 않는 프로퍼티를 삭제하면 아무런 에러 없이 무시됩니다.

```js
let person = {
  name: "zooyaho",
};

person.age = 24;
delete person.age;

console.log(person); // {name: 'zooyaho'}
```

## 6. 계산된 프로퍼티 이름

- 문자열 또는 문자열로 타입 변환할 수 있는 값으로 평가되는 표현식을 사용해 프로퍼티 키를 동적으로 생성할 수 있다.
- ES5은 객테 리터럴 외부에서 대괄호 표기법([..])을 사용하고, ES6은 객체 리터럴 내부에서 사용할 수 있다.

👾 #ES5
: 객체 외부에서 프로퍼티 키를 동적으로 생성!!!

```js
let prefix = "prop";
let i = 0;

let obj = {};

obj[prefix + "-" + ++i] = i;
obj[prefix + "-" + ++i] = i;
obj[prefix + "-" + ++i] = i;

console.log(obj); // { prop-1: 1, prop-2: 2, prop-3: 3 }
```

👾 #ES6
: 객체 내부에서 프로퍼티 키를 동적으로 생성!!!

```js
let prefix = "prop";
let i = 0;

let obj = {
  [`${prefix}-${++i}`]: i,
  [`${prefix}-${++i}`]: i,
  [`${prefix}-${++i}`]: i,
};

console.log(obj); // { prop-1: 1, prop-2: 2, prop-3: 3 }
```

## 7. 메서드 축약 표현

- ES6에서는 메서드를 정의할 때 function키워드를 생략한 축약 표현을 사용할 수 있다.

```js
let person = {
  name: "zooyaho",
  sayHi() {
    console.log("Hi " + this.name);
  },
};

person.sayHi(); // Hi zooyaho
```
