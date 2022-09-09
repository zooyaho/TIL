# 인터페이스

- 구체적 값이 아닌 구조와 형태 지정
- 객체의 타입을 확인하는데 사용
- 컴파일 시 interface의 흔적은 사라짐.

👾

```js
interface Greetable {
  name: string;
  age: number;
  greet(phrase: string): void;
}

/*
type Greetable = {
  name: string;
  age: number;
  greet(phrase: string): void;
}
*/

let user1: Greetable;

user1 = {
  name: "Max",
  age: 25,
  greet(phrase: string) {
    console.log(phrase + " " + this.name);
  },
};
user1.greet("Hi there - I am");
```

## ⭐️ 사용자 정의 type과의 차이점

- 인터페이스는 객체의 구조를 설명하기위해 사용
- 작업 중 인터페이스를 자주 사용하는 이유는 클래스가 인터페이스를 이행하고 준수해야하는 약속처럼 사용할 수 있기 때문이다.

## ● class에 interface 지정

- 1개 이상의 interface를 implements할 수 있다.

```js
interface Greetable {
  name: string;
  age: number;
  greet(phrase: string): void;
}

class Person implements Greetable {
  name: string;
  age = 30;
  constructor(n: string) {
    this.name = n;
  }
  greet(phrase: string) {
    console.log(phrase + " " + this.name);
  }
}
let user1: Greetable;
// let user1: Person; 도 가능

user1 = new Peson("Max");
user1.greet("Hi there - I am");
```

## ● 읽기 전용 속성 - readonly

- **읽기 전용으로 설정**하여 객체가 초기화되면 변경할 수 없도록 한다.
- type에서도 사용할 수 있지만 interface에서 사용하는 경우가 많다.

```js
interface Greetable {
  readonly name: string;
  greet(phrase: string): void;
}

class Person implements Greetable {
  name: string;
  constructor(n: string) {
    this.name = n;
  }
  greet(phrase: string) {
    console.log(phrase + " " + this.name);
  }
}
let user1: Greetable;
user1 = new Peson("Max");
user1.name = 'MArk'; // error
```

## ● 인터페이스 확장 - extends

- interface에 extends를 사용하여 확장한다.
- class에서 extends는 2개 이상의 클래스를 상속받을 수 없다.

```js
interface Named {
  readonly name: string;
}
interface Greetable extends Named{
  greet(phrase: string): void;
}

class Person implements Greetable {
  name: string;
  constructor(n: string) {
    this.name = n;
  }
  greet(phrase: string) {
    console.log(phrase + " " + this.name);
  }
}
let user1: Greetable;
user1 = new Peson("Max");
```

## ● 함수 타입으로서의 interface

```js
// type AddFn = (a: number, b: number) => number;
interface AddFn {
  (a: number, b: number): number;
}

let addFn: AddFn;
addFn = (n1: number, n2: number) => n1 + n2;
```

## ● 선택적 매개변수와 속성 - ?

- 선택적 메서드 : FnName?(): void....

```js
interface Named {
  readonly name: string;
  outputName?: string; // 선택적 속성
  greet?(phrase: string): void; // 선택적 메서드
}

class Person implements Named {
  name?: string;

  constructor(n?: string) { // 선택적 매개변수 설정
  // 기본값이 설정되지 않아서 정의되지 않는 경우에 ?를 추가하거나 기본값을 할당한다.
  // (n: string = '') -> 기본값 할당
    if(n) {
      this.name = n;
    }
  }
}
let user1: Greetable;
user1 = new Peson(); // error가 발생하지 않음
```
