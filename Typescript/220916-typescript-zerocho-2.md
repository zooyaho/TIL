# Utility Types

## Partial

- 속성들을 전부 옵션상태로 만들어줌

```js
interface Profile {
    name: string;
    age: number;
}
type P<T> = {
    [key in keyof T]?: string | number;
}
const user:P<Profile> = {
    name: "jiwoo"
}
```

```js
interface Profile {
    name: string;
    age: number;
}
type P<T> = {
    [key in keyof T]: string | number;
}
const user:Partial<Profile> = {
    name: "jiwoo"
}
```

## Pick

- 설정한 키만 포함한 타입을 지정

```js
interface Profile {
    name: string;
    age: number;
    married: boolean;
}
type P<T> = {
    [key in keyof T]: string | number;
}
const user:Pick<Profile, 'name'|'age'> = {
    name: "jiwoo"
}
/*
interface Profile {
  name: string;
  age: number;
}
*/
```

```js
type P<T,S extends keyof T> = {
    [key in S ]: T[key];
}
const user:P<Profile,'name'|'age'> = {
    name: "jiwoo",
    age: 24
}
```

## Omit

- 설정한 키를 제외한 타입을 지정

```js
interface Profile {
  name: string;
  age: number;
  married: boolean;
}
const user: Omit<Profile, "married"> = {
  name: "jiwoo",
  age: 24,
};
/*
interface Profile {
  name: string;
  age: number;
}
*/
```

👾 Pick, Exclude를 사용하여 Omit구현

```js
type A = Exclude<keyof Profile,'married'>
type O<T, S extends keyof any> = Pick<T, Exclude<keyof T, S>>
const user:O<Profile, 'married'> = {
  name: "jiwoo",
  age: 24
}
```

👉🏻 `S extends keyof any` : S로 key인 타입만 지정하기 위해 제한조건 추가

## Exclude

- 타입을 제외함.

```js
type Exclude<T, U> = T extends U ? never : T;
```

```js
type Animal = 'Cat' | 'Dog' | 'Human';
type Mammal = Exclude<Animal, 'Human'>; // 'Cat' | 'Dog'

interface Profile {
  name: string;
  age: number;
  married: boolean;
}
type A = Exclude<keyof Profile,'married'>
/*
type A = 'name' | 'age'
*/
```

## Extract

- 타입을 추출함.

```js
type Extract<T, U> = T extends U ? T : never;
```

```js
type Animal = 'Cat' | 'Dog' | 'Human';
type Human = Extract<Animal, 'Human'>; // 'Human'

type A = Extract<keyof Profile,'married'>
/*
type A = 'married'
*/
```

## Required

- 옵션 타입을 필수로 바꿔준다.

```js
interface Profile {
  name?: string;
  age?: number;
  married?: boolean;
}

const zooyho: Required<Profile> = {
  name: "jiwoo",
  age: 24,
  married: false,
};
```

## Readonly

```js
interface Profile {
  name?: string;
  age?: number;
  married?: boolean;
}

const zooyho: Readonly<Profile> = {
  name: "jiwoo",
  age: 24,
  married: false,
};

zooyho.name = "mark"; // error
```

## -

- 타입에서 ? or readonly를 빼고 가져와 적용한다.

```js
interface Profile {
  readonly name?: string;
  readonly age?: number;
  readonly married?: boolean;
}

type R<T> = {
  -readonly [key in keyof T]-? : T[key];
}

const zooyho: R<Profile> = {
  name: "jiwoo",
  age: 24,
  married: false,
};
zooyho.age = 25;
```

## Record

- 객체를 표현하는 한가지 방법

👾 Record적용 전

```js
interface Obj {
  [key: string]: number;
}
const a: Obj = { a: 3, b: 5, c: 7 };
```

👾 Record적용 후

```js
const a: Record<string, number> = { a: 3, b: 5, c: 7 };
```

## NonNullable

- null과 undefined타입을 제외하고 가져와 타입을 지정한다.
- type에서 사용하고 interface애서 사용 안함.

```js
type A = string | null | undefined | number;
type B = NonNullable<A>; // string | number
```

```js
type A = string | null | undefined | number;
type N<T> = T extends null | undefined ? never : T;
```
