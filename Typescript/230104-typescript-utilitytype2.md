# Utility Types

## Required

- optional로 된 타입들을 require로 바꿈

```js
interface Profile {
  name?: string;
  age?: number;
  married?: boolean;
}

const zooyaho: Required<Profile> = {
  name: "zooyaho",
  age: 25,
  married: false,
};
```

👾 Require 정의

```js
type Require<T> = {
  [key in typeof T]-?: T[key];
}
```

👉🏻 -? : ?을 제거하는 의미!!

## Readonly

- 수정하지 못하게 함

```js
const zooyaho: Readonly<Profile> = {
  name: "zooyaho",
  age: 25,
  married: false,
};

zooyaho.age = 24; // ❌ Error
```

👾 Readonly 정의

```js
type Readonly<T> = {
  readonly [key in typeof T]: T[key];
}
```

- readonly가 원래 있는 타입일 때 readonly를 없애서 사용하기 위해 `-readonly`를 사용하면 됨

## Record

- 객체를 표현하는 방법

```js
interface Obj {
  [key: string]: number;
}
const a: Obj = { a: 1, b: 2 };

const b: Record<string, number> = { c: 3, d: 4 };
```

👾 Record 정의

```js
type Record<T extends keyof any, S> = {
  [key in T]: S;
}
```

👉🏻 extends keyof any: `const b: Record<Profile, number>` Profile과 같이 객체가 오면 안되니까  
👉🏻객체의 key타입으로는 `string, number, symbol`만 올 수 있으므로!!

## NonNullable

- type에서 null과 undefined만 빼고 사용 가능해짐.
- key에 적용되는 타입이다.

```js
type A = string | null | undefined | number;
const a: NonNullable<A> = null; // ❌ error
type B: NonNullable<A>; // string | number
```

👾 NonNullable 정의

```js
type NonNullable<T> = T extends null | undefined ? never ? T;
```

## Parameters

- 함수의 파라미터 타입을 배열로 가져온다.
- index로 타입 가져올 수도 있다

```js
function zip(x: string, y: number, z: boolean): {x: string, y: number, z: boolean} {
    return {x,y,z};
}

type Params: Parameters<zip>; // [string, number, boolean]
type First = Params[0] // string
```

## ReturnType

- 함수의 return 타입을 배열로 가져온다.
- index로 타입 가져올 수도 있다

```js
function zip(x: string, y: number, z: boolean): {x: string, y: number, z: boolean} {
    return {x,y,z};
}

type Returns: ReturnType<zip>; // [string, number, boolean]
type First = Returns[0] // string
```
