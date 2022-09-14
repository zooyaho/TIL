# 기본 문법

- 타입추론이 잘 되면 추론에 맡기는것이 좋음
- 타입 설정 4가지

1. `:` ex) const name: string = "Mark";
2. `type` ex) type Add = () => number;
3. `interface` ex) interface Minus {}
4. `제네릭` ex) Array<string>

## 빈배열을 조심하자

- 빈배열 선언 시 never[]으로 추론이된다.
- never라는 타입은 일반적인 타입이 올수가 없어진다!
- 그러미그 빈배열 선언 시 필수적으로 타입을 지정해주는 것이 좋다.

## 호환되지 않는 타입의 불가능한 교차 타입 표시

- ex) type Res = string & number; // never
- 명시적으로 never를 사용하지 않은 코드에서 never 타입과 관련된 오류 메시지를 받게 되면 이는 타입스크립트가 일반적으로 타입을 교차하기 때문이다. 타입 안정성을 유지하고 건전성을 보장하기 위해 암묵적으로 이 작업을 수행한다.

## !

- 느낌표 앞의 표현식을 **null, undefined가 아니라는 것을 알린다.**
- ! 사용은 추천하지 않는 방식이므로 if문을 사용 하는것을 권장한다.

## 타입의 대소문자 사용

- 주요 core type은 소문자로 시작하는 타입으로 지정한다.
- String vs string
  - String은 래퍼객체이며, string은 타입이다. 그러므로 string을 사용해야 한다.

## 튜플

- 배열의 아이템 개수를 정하고, 타입을 설정함
- **하지만 push메서드는 막지 못함!!**

```js
type tuple: [string, number] = [ '1', 1 ];
// 둘 다 세번째 요소를 추가하는 코드이지만 push메서드를 에러를 발생시키지 않음
tuple[2] = 2 // ❌ error
tuple.push(2) // 👌
```

## enum

- 0부터 counting함
- `=`을 사용하여 값 지정 가능(문자열도 가능)
- 변수들을 하나의 그룹으로 묶을 때 사용함.
- 🚨 남겨야 할지 안남겨야 할지 모를때는 남기는 것이 좋음.(객체를 사용하여 컴파일 시 해당 내용을 남길 수 있다.)

```js
// js에서 사라짐.
const enum EDirection {
  UP,
  DOWN,
  LEFT,
  RIGHT
}

// ✅ 객체로 생성한다면 사라지지 않음.
const ODirection = {
  UP: 0,
  DOWN: 1,
  LEFT: 2,
  RIGHT: 3
} as const

// ❌
const ODirection = {
  UP: 0,
  DOWN: 1,
  LEFT: 2,
  RIGHT: 3
}
/*
{
UP: number,
DOWN: number,
LEFT: number,
RIGHT: number
}
로 추론 되어 하나하나 타입 지정을 해줘야함.
👉🏻 as const를 사용하여 해당 값들을 상수로 사용하겠다는 의미이다!!
*/
```

```js
const ODirection = {
  UP: 0,
  DOWN: 1,
  LEFT: 2,
  RIGHT: 3
} as const;

// 데이터를 추출하여 타입으로 설정한다.
type Direction = typeof ODirection[ keyof typeof ODirection];

function run(dir: Direction) {}
run(ODirection.UP);
```

👉🏻 `typeof ODirection` ODirection객체를 추출하여 데이터로 사용 가능케한다.<br>
👉🏻 `keyof`는 객체의 key를 반환한다.

## union(유니온) - | vs intersection(인터섹션) - &

### union(유니온)

- 또는
- 모든 가능성을 고려하기 때문에 타입추론이 잘 안된다.
- 여러개 중 하나만 있어도 된다.

```js
type AB = { a: "A" } | { b: "B" };
const a: AB = { a: "A" };
```

### intersection(인터섹션) - &

- 모든 속성이 있어야 한다.

```js
type AB = { a: "A" } & { b: "B" };
const ab: AB = { a: "A", b: "B" };
```

## interface

- 같은 이름으로 여러번 선언할 수 있다. (type은 불가능)
  - 선언할 때마다 합쳐짐!! -> **확장이됨**

## 좁은 타입과 넓은 타입

- 구체적일 수록 좁은 타입이다.
- **넓은 타입에 좁은 타입을 대입할 수 있지만** 좁은 타입에 넚은 타입은 대입하지 못한다.

### 객체리터럴의 잉여 속성 검사

- 객체는 상세 할수록 좁은 타입이다.

👾 객체 리터럴 대입 시 유의사항

```js
type A = { name: string };
type B = { age: number };

type AB = A | B; // 넓은 타입
type C = A & B; // 좁은 타입

const ab: AB = { name: "Mark" };
const c: C = { name: "Mark", age: 25, married: false };
```

👉🏻 넓은 타입에 좁은 타입을 대입했는데 error발생 <br>
👉🏻 객체 리터럴을 바로 대입하면 잉여속성 검사가 실행됨!!

👾 중간에 데이터를 빼주면 error가 발생하지 않음.

```js
const ab: AB = { name: "Mark" };
const obj = { name: "Mark", age: 25, married: false };
const c: C = obj;
```

## void의 사용법

- 함수에 직접적인 void 반환 시에만 return;, return undefined;이다 **이때 void는 반환값이 없어야 한다는 의미이다.**
- 메서드와 콜백함수의 void는 반환값이 있어도 error가 발생하지 않는다. **이때 void는 반환값이 무엇이던 상관하지 않는다는 의미이다.**

```js
// 1
function a(): void {
  return "hi"; // error
}
// 2
interface Human {
  talk: () => void;
}
// 3
function a(callback: () => void): void {}
a(() => {
  return "3";
});
```

### declare

- declare사용 시 함수 타입만 선언할 수 있게 한다.
- 외부에서 만들어진 함수, 변수등을 타입 선언 시 사용한다.

### void는 undefined와 다르다

- 🚨 void형식은 undefined형식에 할당할 수 없지만, undefined형식은 void형식에 할당할 수 있다!!

```js
declare function forEach(arr: number[], callback: (el: number) => void): void;

let target: number[] = [];
forEach([1, 2, 3], (el) => target.push(el)); // number를 반환
forEach([1, 2, 3], (el) => {
  target.push(el);
}); // undefined를 반환
```

👉🏻 콜백함수 반환타입이 void여도 모두 error가 발생하지 않는다.

## unknown과 any

- any를 사용하기보다 unknown을 사용하는 것이 좋음.
- any는 더이상 타입 체크를 하지 않겠다고 선언하는 것과 같음. -> ts를 쓰는 의미가 사라짐.
- unknown도 사용하지 않는게 좋지만
  - 현재는 타입을 모르지만 추후에 타입을 지정하겠다는 의미이다.
  - 타입 포기와는 다름.

👾 unknown

```js
try{ ...
} catch(error) { // 여시서 error는 자동으로 unknown타입을 가지게 됨
  (error as Error).message;
}
```

👉🏻 어떤 타입의 error가 발생할지 모르기때문에 error 메세지 프러퍼티 사용할 때 타입을 지정한다.

## 타입 좁히기(타입 가드)

- 매개면수의 타입에 따른 로직 작성
- 배열타입을 확인 하려면 `Array.isArray(값)`을 사용한다.
- 객체타입가드 작성 시 `in연산자, 타입`을 생성해 구별한다!
- class 타입가드 작성 시 `instanceof`를 사용한다.
- typeof

## 커스텀 타입 가드(is, 형식 조건자)

- 타입을 구분해주는 커스텀함수를 직접 만들수 있게 한다.
- 반환타입에 `is`가 있으면 커스텀 타입 가드 함수이다!
- 커스텀 타입 가드 함수는 if문 안에서 조건문으로 사용한다.
- is가 아니면 타입추론이 안되는 경우도 있다.

![](https://velog.velcdn.com/images/zooyaho/post/402c6a2f-0663-4fda-8d37-5e6ca3066d56/image.png)
👉🏻 is가 있어야만 타입 구분을 할수있다.

## {} 와 Object - v4.8

- {}, Object는 모든 타입을 말한다.
- {}, Object는 null | undefined를 제외한다.
- 실제 객체타입을 말하는 건 object이다.
- object는 지양하고 interface, type, class사용을 권장함.

```js
const x: {} = "hello";
const y: Object = "hi"; // null | undefined 제외!!
const yy: object = "hi"; // error
const z: unknown = "z"; // 모든 타입을 받을 수 있음

// v4.8: unknown = {} | null | undefined
if (z) {
  z;
  // v4.7에서는 z의 타입이 unknown으로 나오지만 v4.8에서는 {}으로 지정된다.
  // if문에서 null | undefined이 걸러지니까 {}으로 지정됨.
}
```

## readonly, 인덱스드 시그니처, 맵드 타입스

👾 readonly

```js
interface A {
  readonly aa: number
}
const a: A = {aa: "Apple"};
a.aa = 'Angle'; // error
```

👾 인덱스드 시그니처

```js
// type A =  { a:string; b:string; c:string; }
type A = { [key: string]: string };
```

👾 맵드 타입스

```js
type Fruit = "apple" | "banana" | "orange";
type FruitInfoA = { [key in fruit]: string };
type FruitInfoB = { [key in fruit]: fruit };
```

## 클래스

```js
class A {
  a: number;
  b: string;
  constructor(a: number, b: string = "123") {
    this.a = a;
    this.b = b;
  }
  x() {}
}
// typeof A == class의 이름인 A를 뜻함
// A는 new A()를 뜻함
const a: A = new A(123);
const b: typeof A = A;
```

### private

- TS의 private은 JS의 #과 다름
- 컴파일 시 private은 public으로 변환

```js
class A {
  #a: number = 123;
  private b: string = "123";
  x() {
    console.log( this.#a, this.b );
  }
}
const a: A = new A();
```

### public, private, protected

- 실제 JS에서는 public, private는 사라짐

|             | public | private | protected |
| :---------: | :----: | :-----: | :-------: |
| 클래스 내부 |   O    |    O    |     O     |
|  인스턴스   |   O    |    X    |     X     |
| 상속 클래스 |   O    |    X    |     O     |

## 제네릭

- 제네릭도 기본값을 줄 수 있음.
- TS가 추론을 못할때 기본값을 부여하면 된다.(React에서 jsx문법과 혼동되어 추론이 잘 안될 수 있음)

```js
function add<T>(a: T, b: T): T {
  return a + b;
}
// T에 타입 제한 걸기
function minus<T extends number>(a: T, b: T): T {
  return a + b;
}
```

```js
<T extends {...}>
<T extends any[]>
<T extends (...args: any) => any> // 모든 함수를 지정할 때 any사용해도됨. 함수라는 제한만 거는것이기 때문
<T extends abstract new (...args: any) => any> // class 자체를 넣을 경우
```

```js
// <T,> : ,만 둬도 가능해짐
// const add = <T,>(a: T, b: T): T {
//   return a + b;
// }
const add = <T = unknown>(a: T, b: T): T {
  return a + b;
}
add(1,2); // a,b는 number로 기본 타입에서 덮어씀
```

👉🏻 React에서 jsx문법과 혼동되어 추론이 잘 안될 수 있어 T에 기본타입을 지정한다.
