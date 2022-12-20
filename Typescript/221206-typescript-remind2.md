## 7. 튜플

- 튜플타입의 배열에 새로운 요소 추가 시 index를 사용한 추가는 불가능 하지만 push()메서드를 활용한 추가는 가능하다!

```js
const tuple: [string, number] = ["1", 1];
tuple[2] = "hello"; // error
tuple.push("hello"); // 가능
```

## 8. enum

- 차례대로 0,1,2,...가 부여됨.
- UP에 3 지정 시 3,4,5,6이 됨.
- **문자열** 지정도 가능
- js로 변환 시 없어짐.
- 각각의 값 지정도 가능!

```js
// enum 사용
const enum EDirection {
  Up,
  Down,
  Left,
  Right = 5,
}
const a = EDirection.Up; // a = 0;
const b = EDirection.Right; // b = 5;

// 객체 사용
const ODirection = {
  Up: 0,
  Down: 1,
  Left: 2,
  Right: 3,
} as const // ⭐️ readonly로 수정 못하게 고정이 됨!!

const ODirection: {Up: 0, Down: 1, Left: 2, Right: 3} = {
  Up: 0,
  Down: 1,
  Left: 2,
  Right: 3,
}
```

🔥 **enum과 객체를 타입으로 사용할 경우**

```js
// ⭐️ enum은 바로 타입으로 사용할 수 있다.
function walk(dir: EDirection){}
walk(EDirection.Left);

// ⭐️ 객체는 타입을 생성하여 사용한다.
type Direction = typeof ODirection[keyof typeof ODirection]; //  0 | 1 | 2 | 3 => value들만 가져오게 됨.

function run(dir: Direction) {}
run(ODirection.Right);
```

## 9. keyof, typeof

- `keyof typeof obj` : key들로만 타입 생성
- `typeof obj[keyof typeof obj]` : value들로만 타입 생성

```js
const obj = { a: '123', b: 'hello', c: 'wold'};
type key = keyof typeof obj; // key = 'a' | 'b' | 'c';
// typeof obj => {a: string, b: string, c: string}
type value = typeof obj[keyof typeof obj]; // '123' | 'hello' | 'wold'
```

## 10. union(|), intersection(&)

- `&`: type alias에서 두 `객체타입을 합칠 때` 주로 사용!

```js
type A = { hello: "wold" } & { zoo: "yaho" };
const a: A = { hello: "wold", zoo: "yaho" }; // 🆗
const b: A = { hello: "wold" }; // ❌

type B = { hello: "wold" } | { zoo: "yaho" };
const a: B = { hello: "wold", zoo: "yaho" }; // 🆗 ⭐️ 중요!!
const b: B = { hello: "wold" }; // 🆗
```

## 11. 상속(extends)

1. `type alias`에서의 상속은 &을 사용한다.

```js
type Animal = { breath: true };
type Human = Animal & { think: true };

const mark: Human = { breath: true, think: true };
```

2. `interface타입`은 extends를 사용한다.

```js
interface A {
  breath: true;
}
interface B extends A {
  think: true;
}

const mark: B = { breath: true, think: true };
```

3. `interface타입`은 **같은 이름으로 여러번 선언이 가능하여 확장을 할 수 있다.**
   ✔️ type은 같은 이름으로 여러번 선언이 불가능함!!

```js
interface A {
  talk: () => void;
}
interface A {
  eat: () => void;
}
interface A {
  shit: () => void;
}

const a: A = { talk() {}, eat() {}, shit() {} };
```
