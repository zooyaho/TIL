## 17. 타입 좁히기(타입 가드)

- 함수의 매개변수 타입이 여러개일 경우 if(조건문)을 사용하여 타입을 지정하면 해당 타입으로 추론함.

```js
function numOrNumArr(a: number | number[]) {
  let answer;
  if (Array.isArray(a)) {
    // Array.isArray: 매개변수가 배열인지 확인하는 메서드
    answer = a.map((n) => n + 1);
  } else {
    // 매개변수가 number일 경우
    answer = a.toFixed(2);
  }
  console.log(answer);
}
numOrNumArr(123);
numOrNumArr([1, 2, 3]);
```

- 객체 타입 경우 타입의 속성을 가지고 구별할 수 있음!!

```js
type A = { type: "a", aaa: "aaa" };
type B = { type: "b", bbb: "bbb" };
type C = { type: "c", ccc: "ccc" };

function typeCheck(a: A | B | C) {
  if (a.type === "a") {
    console.log(a.aaa);
  } else if (a.type === "b") {
    console.log(a.bbb);
  } else {
    console.log(a.ccc);
  }
}
```

## 18. 커스텀 타입 가드(is, 형식 조건자)

- 타입을 구분해주는 커스텀 함수를 만들어 사용한다!
- is가 있다면 커스텀 타입가드 함수이며, 이 함수는 if문에서 사용한다.

```js
interface Cat {meow: number}
interface Dog {bow: number}
function catOrDog(a: Cat | Dog): a is Dog {
  if((a as Cat).meow)
    return false;
  return true;
}
function pet(a: Cat | Dog){
  if(catOrDog(a)) {
    console.log(a.bow);
  }
  if('meow' in a){
    console.log(a.meow);
  }
}

```

## 19. {}와 Object(v4.8.0)

- 모든 타입을 말함
- object가 객체 타입을 말하지만 지양해야하고, 객체 타입은 interface, type, class를 사용하는 것이 좋다!!

### ⭐️ unknown = {} | null | undefined

- (v4.8.0)에서 위 공식이 성립되었다!!
- {}은 null 과 undefined 대입이 되지 않는다!!(unknown과의 차이점)

```js
const x: {} = "hello";
const y: Object = "hi";
const xx: object = "hi"; // ❌
const yy: object = { hello: "wold" };
const z: unknown = "hi";

if (z) {
  // unknown = {} | null | undefined
  z; // z = {} -> null과 undefined가 걸러짐
} else {
  z; // z = null | undefined
}
```

## 20. 제네릭

- 제네릭에 extends로 타입에 제한을 줄 수 있음.

```js
function add<T extends string | number>(x: T, y: T): T{
  return x + y;
}
// 제네릭 변수 각각 타입 지정 가능
function minus<T extends number, K extends number>(x: T, y: K) {
  return x - y;
}
add('1', '2'); // '12'
add(1, 2); // 3
add(true, false) // ❌
```

### 제네릭에서 콜백함수 제한

- `<T extends (...args: any) => any>` 모든 함수를 가리킴

```js
function add<T extends (a: string) => number>(x: T): T { return x; }

add((a)=> +a);
```

### arrow fn 에서 제네릭 표현하기

- JSX문법과 헷갈려 제네릭 인식을 못하기 때문에 `<T = unknown>`, `<T extends unknown>`로 기본 타입을 지정해야한다(추론을 못할 때 기본 타입을 지정한다)

```js
const add = <T = unknown>(x: T, y: T) => ({x, y})
const add = <T extends unknown>(x: T, y: T) => ({x, y})
```
