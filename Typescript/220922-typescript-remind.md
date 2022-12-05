>

- 타입은 최대한 자세하게 정의하는 것이 좋음
- TS가 JS로 변환할 때 :(콜론), type, interface, 제네릭, as, enum 부분은 사라짐.

## 1. 화살표 함수 타입지정

```js
interface AddI {
  (a: number, b: number): void;
}
type AddT = (a: number, b: number) => void;
const plus: AddI = (a, b) => {
  console.log(a + b);
};
plus(1, 3);
```

## 2. 튜플

: 고정된 길이의 배열 타입 지정

```js
const arr: [number, number, string] = [1, 2, "3"];
```

## 3. 함수 타입 지정과 선언

```js
function add(x:number, y:number): number; // 함수 타입 지정
function add(x, y) { return x + y; } // 함수 선언
```

## 4. 타입을 강제로 다른 타입으로 설정(as)

```js
let a = 123;
a = 'hello' as unknown as number; // string타입을 강제로 number타입으로 변경
```

## 5. never 타입

- ⭐️ **빈배열 선언 시 never타입으로 지정되므로** 반드시 미리 타입을 지정해야함!

```js
const arr = [];
arr.push("hello"); // error: string형식을 never형식에 할당할 수 없습니다!
```

## 6. 최대한 !대신 if문을 사용한다.

- ⭐️ **!** : null이나 undefined가 아님을 보증하는 역할.

```js
// if문 사용하여 null이 아님을 지정
const head = document.querySelector('#header'); // Element | null
if(head){ head.innderHTML = 'hello'; }

// !사용
const head = document.querySelector('#header')!; // Element
```
