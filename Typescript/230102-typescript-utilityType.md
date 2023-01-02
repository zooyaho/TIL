# Utility Types

## Partial

- 타입들을 모두 optinal로 만들어 줌

```js
interface Profile {
  name: string;
  age: number;
  married: boolean;
}

const zooyaho: Partial<Profile> = {
  name: 'zooyaho',
}

// Partial 정의
type P<T> = {
  [key in keyof T]?: T[key];
}
/*
interface Profile {
  name?: string;
  age?: number;
  married?: boolean;
}
*/

const zooyaho: P<Profile> = {
  name: 'zooyaho',
}
```

```js
const zooyaho: P<Profile> = {};
```

👉🏻 이처럼 아무것도 정의 하지 않아도 error가 발생하지 않기 때문에 많이 사용하진 않는다. Pick, Omit은 사용할 타입을 정확하게 정의하기 때문이다!

## Pick

- 가져올 타입을 정해서 사용

```js
const newZooyaho: Pick<Profile, "name" | "age"> = {
  name: "zooyaho",
  age: 25,
};
```

👾 Pick 정의

```js
type Pick<T, S extends keyof T> = {
  [key in S]: T[key];
}
```

## Extract

- T에서 U만 포함한 타입 사용
- key만 추출하여 타입 지정함.

```js
type Extract<T, U> = T extends U ? T : never;
```

## Exclude

- T에서 U를 제외한 타입 사용
- key만 추출하여 타입 지정함.

👾 Exclude 정의

```js
type Exclude<T, U> = T extends U ? never : T;
```

```js
type E = Exclude<keyof Profile, "married">;
// type E = "name" | "age"

type Animal = "cat" | "Dog" | "Human";
type A = Exclude<Animal, "Human">;
// type A = "cat" | "Dog"
```

## Omit

- 타입에서 사용하지 않을 타입을 지정해서 사용

```js
const newZooyaho: Omit<Profile, "married"> = {
  name: "zooyaho",
  age: 25,
};
```

👾 Omit 정의

```js
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
//  K extends keyof any : key값만 가능하기 떄문!
// keyof any : string | number | symbol 만 가능
```
