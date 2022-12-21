## 12. 좁은타입과 넓은타입

- 좁은 타입을 넓은 타입에 대입하는건 가능하지만 넓은 타입을 좁은 타입에 대입하지 못한다.
- `객체타입은 자세할수록 좁은타입이다.`
- 하지만 객체타입에서 좁은타입을 넓은타입에 대입할 경우 오류가 발생하는데 이는 `객체 리터럴 검사`로 인해 `잉여속성검사`를 추가적으로 검사하기 때문이다. 중간에 해당 객체를 생성하여 대입하면 잉여속성검사를 하지 않는다!!

```js
type A = { name: string };
type B = { age: number };
type AB = A | B;
type C = A & B;

const ab: AB = { name: "zooyaho" };
const d: C = { name: "zooyaho", age: 25, married: false }; // error 객체 리터럴 검사의 잉여속성검사로 인한 error
const obj = { name: "zooyaho", age: 25, married: false };
const c: C = obj; // 잉여 속성 검사 하지 않음!!
```

## 13. void의 두가지 사용법

- **function으로 선언한 것과 메서드로 선언할때 void의 역할이 다르다**
- `매개변수로 선언한 void`, `function으로 선언한 void`, `메서드로 선언한 void` 크게 세가지가 있다.
- `매개변수로 선언한 void`, `메서드로 선언한 void` :
  - 🔥 return값이 있어도 된다!!
  - return 값이 있어도 error가 발생하지 않는 이유는 `return값을 사용하지 않겠다는 의미(return 값을 무시함)이기 때문이다`
  - `function으로 선언한 void`는 return값이 있을 경우 error를 발생시킨다.

```js
function a(callback: () => void): void {}
a(() => {
  return "3";
}); // 🆗

interface Human {
  talk: () => void;
}
const human: Human = {
  talk() {
    return; // 🆗
    // return undefined; 🆗
    // return 'abc'; 🆗
  },
};
```

## 14. declare

- 함수 타입선언 후 바로 구현부를 작성하지 않을 경우 함수 타입 앞에 declare를 작성한다. js로 변환 시 해당 함수 타입은 사라진다.

```js
declare function forEach(
  arr: number[],
  callback: (el: number) => undefined
): void;
// declare 미 작성 시 error발생

let target: number[] = [];
forEach([1, 2, 3], (el) => target.push(el));
```

## 15. return타입에서 void와 undefined차이

- `매개변수로 선언한 void`, `메서드로 선언한 void` 에서 return 타입을 void설정 시 리턴 값이 있어도 error가 발생하지 않지만 **return 타입을 unedefined로 설정할 경우 리턴 값이 있으면 error가 발생한다!**

## 16. unKnwon과 any(그리고 타입 대입 가능표)

- `any`: 타입선언 포기와 같아 사용하게 되면 타입 추정을 하지 않음.
- `unknown`: 추후에 타입을 지정한다는 의미. `as unknown`으로 강제 타입 지정이 가능 하다.

```js
try {
} catch(error) { // unknown타입이 설정됨
  (e as Error).message // 타입을 설정하고 사용해야 error 발생하지 않음.
}
```

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
