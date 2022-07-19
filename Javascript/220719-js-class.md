220719 - TIL

- fastcampus Megabyte School : 프론트엔드 개발 취업 연계 과정 강의에서 복습한 내용을 간단하게 정리 하였습니다.

# JS 클래스

## 1. 생성자 함수(prototype)

```js
function User(first, name) {
  this.firstName = first;
  this.lastName = last;
}
user.prototype.getFullName = function () {
  return `${this.firstName} ${this.lastName}`;
};
const amy = new user("Amy", "Clarke");
// amy 인스턴스 생성
console.log(amy.getFullName()); // user객체의 getFullName메서드를 참조하여 실행함.
```

> 참고 01. 생성자 함수(prototype) - Ch 4. JS 클래스

## 2. this

- 일반 함수는 **호출 위치**에 따라 this 정의
- 화살표 함수는 **자신이 선언된 함수 범위**에서 this 정의

#01 - 객체의 메서드에서 this

```js
const zooyaho = {
  name: 'Zooyaho',
  normal: function () {
    console.log(this.name);
  }
  arrow: () => {
    console.log(this.name);
  }
}
zooyaho.normal(); // Zooyaho
zooyaho.arrow(); // undefined

const amy = {
  name: 'Amy',
  normal: zooyaho.normal, // 함수 자체가 할당
  arrow: zooyaho.arrow
}
amy.normal(); // Amy
amy.arrow(); // undefined

```

#02 - 생성자 함수의 prototype메서드에서 this

```js
function User(name){
  this.name = name;
}
User.prototype.normal = function() {
  return ${this.name};
}
User.prototype.arrow = () => {
  return ${this.name};
}
const amy = new User('Amy');
// amy 인스턴스 생성
console.log(amy.normal()); // Amy
console.log(amy.arrow()); // undefined
```

#03 - 콜백함수 안에서의 this

```js
const timer = {
  name: "Amy",
  timeout: function () {
    setTimeout(function () {
      console.log(this.name);
    }, 2000);
  },
};
timer.timeout(); // undefined
```

👉🏻 콜백함수를 호출한 것이 setTimeout이기 때문에 콜백함수는 setTimeout()함수 내에서 실행되어 undefined를 반환함.

```js
const timer = {
  name: "Amy",
  timeout: function () {
    setTimeout(() => {
      console.log(this.name);
    }, 2000);
  },
};
timer.timeout(); // Amy
```

👉🏻 콜백함수 자신이 선언된 함수 범위가 function() {}이며, function의 this는 timer를 가리키고 있기때문에 this.name은 Amy가 출력된다.

💡 콜백함수는 화살표함수를 사용하는것이 좋다!

> 참고 02. this - Ch 4. JS 클래스

## 3. ES6 Classes

#01

```js
const zooyaho = {
  name: 'zooyaho',
  normal(){
    console.log(this.name);
  }
  normal2: function(){
    console.log(this.name);
  }
}
```

👉🏻 normal(){}과 normal2: function(){} 같다!

#02-class

```js
class User() {
  constructor(first, last) {
    this.firstName = first;
    this.lastName = last;
  }
  getFullName() {
    return `${this.firstName} ${this.lastName}`;
  }
  /*
  user.prototype.getFullName = function () {
    return `${this.firstName} ${this.lastName}`;
  };
  */
}
```

> 참고 03. ES6 Classes - Ch 4. JS 클래스
