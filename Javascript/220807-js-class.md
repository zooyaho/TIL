220807 - TIL

> 참고 모던 자바스크립트 Deep Dive

# 클래스

- 클래스는 값으로 사용할 수 있는 일급 객체이다.
- 클래스는 함수로 평가된다.
- 클래스 선언문으로 정의한 클래스는 함수 선언문과 같이 소스코드 평가 과정, 즉 런타임 이전에 먼저 평가되어 함수 객체를 생성한다. 이때 함수 객체는 생성자 함수로서 호출할 수 있는 함수, 즉 constructor다
- 함수정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.
- 클래스는 정의 이전에 참조할 수 없다.
- 클래스 표현식으로 정의된 클래스의 경우 식별자를 사용해 인스턴스를 생성해야 한다. 클래스 이름으로 인스턴스 생성할 경우 에러를 발생한다. 클래스 이름은 클래스 몸체 내부에서만 유효한 식별자이다!

## 1️⃣ constructor() 메서드

- 인스턴스를 생성하고 초기화 하기 위한 특수한 메서드이다.
- constructor 프로퍼티는 클래스 자신을 가리키고 있다.
- constructor 내부에서 this에 추가한 프로퍼티는 인스턴스 프로퍼티가 된다.
- 메서드로 해석되는 것이 아닌 클래스가 평가되어 생성한 함수 객체 코드의 일부가 된다!!
- constructor를 생략하면 빈 constructor가 암묵적으로 정의된다. 빈 constructor는 빈 객체를 생성한다.
- 별도의 반환문을 가지고 있으면 안된다! new연산자와 함께 클래스가 호출되면 생성자 함수와 동일하게 this를 암묵적으로 반환한다!

## 2️⃣ 프로토타입 메서드

- 생성자 함수와 다르게 클래스의 prototype프로퍼티에 메서드를 추가하지 않아도 기본적으로 프로토타입 메서드가 된다!

```js
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  sayHi() {
    consoel.log(`Hi My name is ${this.name}`);
  }
}

const me = new Person("Lee");
me.sayHi(); // Hi My name is Lee
```

![](https://velog.velcdn.com/images/zooyaho/post/4075612d-08ca-4a07-b152-e23887fa9c7a/image.jpg)

## 3️⃣ 정적 메서드

- 인스턴스를 생성하지 않아도 호출할 수 있는 메서드를 말한다.
- static키워드를 붙이면 된다!
- 정적 메서드는 인스턴스로 호출할 수 없으므로, 인스터스로 클래스의 메서드를 상속 받을 수 없다!

👾 생성자 함수의 정적 메서드 생성

```js
function Person(name) {
  this.name = name;
}
Person.sayHi = function () {
  console.log("Hi");
};
Person.sayHi(); // Hi
```

👾 class에서의 정적 메서드 생성

```js
class Person(){
  constructor(name){
    this.name = name;
  }

  // 정적 메서드
   static sayHi(){
    consoel.log(`Hi`);
  }
}
Person.sayHi(); // Hi
```

👾 정적 메서드는 인스턴스로 호출할 수 없다

```js
class Person(){
  constructor(name){
    this.name = name;
  }

  // 정적 메서드
   static sayHi(){
    consoel.log(`Hi`);
  }
}
const me = new Person('Lee');
me.sayHi(); // TypeError
```
