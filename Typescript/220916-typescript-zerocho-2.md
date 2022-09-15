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
};
/*
interface Profile {
  name: string;
  age: number;
}
*/
```

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

- T에서 U타입을 빼는 것

```js
interface Profile {
    name: string;
    age: number;
    married: boolean;
}
type A = Exclude<keyof Profile,'married'>
/*
interface Profile {
  name: string;
  age: number;
}
*/
```

```js
type Exclude<T, U> = T extends U ? never : T;
```

## Extract

- 타입을 추출함.

```js
type Extract<T, U> = T extends U ? T : never;
```
