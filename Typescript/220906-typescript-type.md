## 🔵 core types

1. string
2. number
3. boolean
4. object
5. Array
6. Tuple
7. Enum
8. Any
9. undefined
10. Function
11. unKnown
12. never

### ● 튜플(Tuple)

- **타입과 길이가 고정된 배열**
- 🚨 push는 예외적으로 튜플에서 허용하는 메서드이다.

👾 role

```js
let person: {
  name: string,
  age: number,
  role: [number, string],
} = {
  name: "zooyaho",
  age: 25,
  role: [2, "admin"],
};

person.role.push("author"); // error를 발생하지 않음!
```

### ● 열거형(Enum)

- 사용자 지정 방식
- 식별자들을 중괄호 쌍 안에 넣는 방식이며 열거 목록을 제공
- 이 라벨들은 0부터 시작하는 숫자로 변환된다.
- 시작 값을 =을 사용하여 타입불문 값을 변경할 수 있다.

👾
: role의 id는 각각 관리자는 0, 읽기 전용 사용자는 1, 작성자는 2

```js
// 0, 1, 2가 각각 할당됨
enum Role { ADMIN, READ_ONLY, AUTHOR };
/*
enum Role { ADMIN = 5, READ_ONLY, AUTHOR };
-> AUTHOR은 각각 5, 6, 7이 할당됨
*/

let person = {
  name: 'zooyaho',
  age: 25,
  role: Role.ADMIN,
}

if(person.role === Role.ADMIN) {
  console.log("is Admin!!");
}
```

### ● Any

- 가장 유연한 타입이며 모든 종류의 값을 저장할 수 있다.
- any사용 시 컴파일러가 검사할 부분이 없어져 컴파일러가 작동하지 않게 됨.
- 타입스크립트가 주는 장점을 모두 상쇄시켜 바닐라 자바스크립트를 쓰는 것과 다를 바 없게 된다.
- **let은 기본적으로 any타입이 지정된다.**

## 🔵 리터럴 타입

- 정확한 값을 가지는 타입
- string이나 number와 같은 핵심 타입을 기반으로 함.
- 유니언 타입의 맥락에서도 사용할 수 있으며, 코드에 하드코딩할 수 있는 정확한 값만 허용하는게 아닌 두개 이상의 가능한 값을 가지려는 경우 사용함.

## 🔵 별칭(ALias)

- **type키워드를 사용하여 재사용 가능한 타입 별칭을 생성하여 사용**
- type 사용자 정의 타입 | 알리어스 이름
- 유니온 타입, 복잡한 객체 타입 등에도 별칭 사용

```js
type Combinable = number | string;

function combine(input1: Combinable, input2: Combinable){...}
```

## 🔵 함수 타입 정의

### ● 함수의 반환 타입 정의

- `:`을 사용하여 함수 반환타입을 정의할 수 있다.
- undefined를 비롯해 아무것도 반환하지 않으면 `:void`를 사용해야 한다.
- 함수가 아무것도 반환하지 않으면(return이 없으면) 자동으로 undefined를 반환한다.
- 하지만 `return이 없는 것`과 `return undefined`은 타입 지정 시 다르다. **`return이 없는 것` | `return;`은 `:void`로, `return undefined;` 은 `:undefined`으로 정확히 명시해야 한다.**

### ● 함수 타입 정의(Function)

- `let fn : Function`으로 식별자에 **Function타입을 지정한다.**
- **함수 타입은 함수의 매개변수와 반환값에 관련된 함수를 설명하는 함수로 화살표함수 표기법으로 설정할 수 있다.**

👾 Function타입 지정

```js
function add(n1:number, n2:number){...}
let combineFn: Function;

combineFn = 5; // error
combineFn = add;
```

👾 화살표함수 표기법으로 함수타입 지정

```js
// 매개변수를 취하지 않는 number를 반환하는 함수 타입
// let combineFn: () => number;
let combineFn: (a: number, b: number) => number;

combineFn = 5; // error
combineFn = add;
```

### ● 함수 타입 및 콜백

- **콜백 타입의 반환 타입을 void로 설정했을 때, 콜백함수에 반환값을 설정해도 error가 발생하지 않도 return이 무시된다.** 이것은 타입스크립트의 버그가 아니다.
- **콜백 함수는 자신이 전달되는 인수가 반환 값을 기대하지 않는 경우에도 값을 반환할 수 있다.**
- 함수 타입 지정은 기본적으로 매개변수에는 엄격하지만, 반환 타입에는 크게 간여하지 않는다.

```js
function addAndHandle(n1: number, n2: number, cb: (num: number) => void) {
  const result = n1 + n2;
  cb(result);
}

addAndHandle(2, 3, (result) => {
  console.log(result);
});
/*
addAndHandle(2, 3, (result) => {
  console.log(result);
  return result; // error가 발생하지 않음!!!
})
👉🏻 return 작업이 무시된다!!
*/
```

## 🔵 알수 없는 타입(unknown)

- 에러 발생 없이 어떤 값이든 저장할 수 있다.
- any와는 다르게 좀 더 제한적이다!

```js
let userInput: unknown;
let userName: string;

userInput = 5;
userInput = "Max";
userName = userInput; // error
```

👉🏻 unknown은 string으로 인식되지 않아 error가 발생된다!  
👉🏻 unknown을 any로 변경하면 error가 발생되지 않는다.

👾 추가 적인 타입 검사를 통해 할당할 수 있다.

```js
let userInput: unknown;
let userName: string;

if(typeOf userInput === 'string') {
	userName = userInput;
}
```

## 🔵 절대 타입(never)

- 아무것도 반환하지 않는다는 것을 확실히 하기 위해 never를 명시적으로 설정할 수 있다.
- 코드 품질의 관점에서 의도를 더 분명히 할 수 있다.
- 기본적으로 아무것도 반환하지 않고, 스크립트나 스크립트의 일부를 충돌시키거나 망가트리기 위한 것임을 코드를 읽는 개발자가 알게 할 수 있다.

```js
function generateError(message: string, code: number) {
  throw { message: message, errorCode: code };
}
generateError("에러 발생", 500);
```

👉🏻 이 함수는 never를 반환하며 반환 값을 생성하지 않기때문에 void 함수이기도 하다.
