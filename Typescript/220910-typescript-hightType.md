## 🔵 유니온 타입에 따른 타입가드

- 유니온 타입이 지닌 유연성을 활용할 수 있게 하며 런타임 시 코드를 정확하게 해준다.
- **특정 속성이나 메서드를 사용하기 전에 그것이 존재하는지 확인하거나 타입을 사용하기 전에 이 타입으로 어떤 작업을 수행할 수 있는지를 확인하는 개념 또는 용어이다.**
- `in`을 사용하여 사용자 정의 타입을 구분하여 작성할 수 있다.
- class를 유니온으로 정의한 타입 사용 시 `instanceof`를 사용하여 구분해준다.

```js
type Combinable = string | number;
type Numeric = number | boolean;
type Universal = Combinable & Numeric;

function add(n1: Combinable, n2: Combinable) {
  if (typeof n1 === "string" || typeof n2 === "string") {
    return n1.toString() + n2.toString();
  }

  return n1 + n2;
}
```

👉🏻 `if (typeof n1 === "string" || typeof n2 === "string")` 이 문을 타입 가드라고 한다.
👉🏻 하지만 typeof으로는 사용자 정의 타입을 구분할 수 없다.

```js
type UnknownEmployee = Employee | Admin;
function printEmployeeInformation(emp: UnknownEmployee) {
  if ("privileges" in Employee) {
    // 타입 가드
    console.log("privileges: " + emp.privileges);
  }
  if ("startDate" in Admin) {
    // 타입 가드
    console.log("startDate: " + Admin.startDate);
  }
}
```

## 🔵 구별된 유니언 사용하기

- type속성을 사용하여 구별

```js
interface Bird {
  type: "bird";
  flyingSpeed: number;
}
interface Horse {
  type: "horse";
  runningSpeed: number;
}

type Animal = Bird | Horse;

function moveAnimal(animal: Animal) {
  switch (animal.type) {
    // 자동완성으로 통해 정확한 구별을 할 수 있음.
    case "bird": {
      console.log(animal.flyingSpeed);
      break;
    }
    case "horse": {
      console.log(animal.runningSpeed);
      break;
    }
  }
}

moveAnimal({ type: "bird", flyingSpeed: 200 });
// moveAnimal({type:'bird',runningSpeed:100 }); error발생
```

## 🔵 형 변환(typecasting)

- 형 변환이란 타입스크립트가 직접 감지하지 못하는 특정 타입의 값을 타입스크립트에 알려주는 역할을 한다.
- 타입스크립트는 HTML 파일을 살펴보고 분석하지 못한다.

```js
const inputEl = document.getElementById("input-field");

inputEl.value = "hello"; // error발생
```

👉🏻 value가 HTMLElement타입에 존재하지 않는 error가 발생한다.  
👉🏻 기본적으로 모든 HTML 요소가 타입으로서 갖는 제네릭 타입이 특정 HTMl요소인 속성을 지원하지 않는다.  
👉🏻 따라서 inputEl가 단순히 null이 아닌 HTML inputEelement 타입임을 TS에게 알려야 한다. 이때 형변환을 사용한다.

### ● 형변환 방법

: 변환하고자하는 요소 앞이나 타입스크립트에 타입을 알려주고자 하는 위치 앞에 타입을 추가한다.

#### 1️⃣

```js
const inputEl = <HTMLInputElement>document.getElementById("input-field");

inputEl.value = "hello"; // error발생
```

👉🏻 `<HTMLInputElement>`은 현재 사용 중인 타입에 대한 정보를 전달하기 위해 사용하는게 아닌 **빌드 도구와 리액트로 구문 분석되어 결과적으로 화면에 렌더링하고자 하는게 무엇인지 알아내는데 사용한다.**

#### 2️⃣

```js
const inputEl = document.getElementById("input-field")! as HTMLInputElement;

inputEl.value = "hello";
```

👉🏻 jsx구문과 구별하기 위해 as키워드를 사용한다.  
👉🏻 `document.getElementById("input-field")!`는 HTMLInputElement타입의 값을 반환하므로 에러를 발생시키지 않는다.

### ● !

- 느낌표 앞의 표현식을 **null로 반환하지 않겠다고 타입스크립트에 인식시킨다.**
- null반환하지 않을 것이라는 확신이 들지 않으면 if문을 사용 한다.

👾 느낌표의 대안

```js
const inputEl = document.getElementById("input-field");

if (inputEl) {
  // 표현식의 결과에 대한 값에 접근해야 한다.
  (inputEl as HTMLInputElement).value = "hello";
}
```

## 🔵 인덱스 속성

`[prop: type]: type`

- 인덱스 타입을 설정한다.
- 🔥 **유연성을 제공하기에 사용하고자 하는 속성 이름과 필요한 속성의 개수를 미리 알 필요가 없다.**

👾 사용자 입력의 유효성 검사를 하는 경우

```js
interface ErrorContainer {
  // 반드시 문자열의 키, 값을 지녀야 한다.
  [prop: string]: string; // 인덱스 타입을 설정!!
  // [prop: number]: string;
}

const errorBag: ErrorContainer = {
  // 1: "Not a valid email", 👉🏻 1도 문자열로 해석되기 때문에 문제없다.
  email: "Not a valid email",
  username: "Not a valid username",
};
```

## 🔵 함수 오버로드

- 다양한 매개변수를 지닌 함수를 호출하는 여러 가지 가능한 방법을 사용하여 함수 내에서 작업을 수행할 수 있게 해줍니다.

👾 #01

```js
type Combinable = string | number;

function add(n1: Combinable, n2: Combinable) {
  if (typeof n1 === "string" || typeof n2 === "string") {
    return n1.toString() + n2.toString();
  }

  return n1 + n2;
}

const result = add("Max", " Siri");
// 이때 문자열이 반환된다는 것을 TS는 알지 못해 문자메서드사용을 하지못한다.
result.split(" "); // error
```

👉🏻 `const result = add('Max',' Siri') as string;`
: as string으로 형변환을 명시적으로 작성하면 되지만 코드가 길어진다는 단점이 있다.  
👉🏻 이때 함수 오버라이드를 사용하면 된다.

👾 #02 - 함수 오버라이드 사용

```js
type Combinable = string | number;

function add(n1: number, n2: number): number;
function add(n1: string, n2: string): string;
function add(n1: number, n2: string): string;
function add(n1: string, n2: number): string;
function add(n1: Combinable, n2: Combinable) {
  if (typeof n1 === "string" || typeof n2 === "string") {
    return n1.toString() + n2.toString();
  }
  return n1 + n2;
}

const result = add("Max", " Siri");
result.split(" "); // 문자열을 반환하는것을 인식함
```

## 🔵 선택적 체이닝

- 선택적으로 사용할 요소 다음에 ?를 추가한다.
- typescript 3.7버전 이상부터 사용 가능하다.

👾 #01
: 객체 데이터의 중첩된 속성과 객체에 안전하게 접근할 수 있게한다.
: 백엔드에서 데이터를 가져올 경우, 일부 데이터가 개발자가 작성한대로 불러와지지 않았을 때 선택적 체이닝을 사용하여 에러를 방지한다.

```js
const fetchedUserData = {
  id: "u1",
  name: "job",
  // job: { title: "CEO", description: "My own company"}
};
console.log(fetchedUserData?.job.title); // error
```

## 🔵 Null 병합

- `??` : null, undefuned일 경울 폴백값을 저장한다.
- ts는 dom api를 통해 가져온 요소는 null인지 판단하지 못한다.
- null이라면 폴백(fallback)값을 저장하도록 한다.

```js
const a = null;
console.log(a ?? "DEFAULT"); // DEFAULT
```
