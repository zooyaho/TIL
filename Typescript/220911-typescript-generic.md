# 제네릭(generic)

- <>에 타입을 지정한다.
- 제네릭 타입 정보를 사용하여 수행할 수 있는 작업에 유용하다.
- **정확히 어떤 타입이 될지 모른다는 추가 정보를 타입스크립트에 제공한다.**
- 타입 안전성과 결합된 유연성

👾 배열 제네릭 타입

```js
const arr: Array<string> = []; // === string[]
```

👾 프로미스 제네릭 타입

```js
const promise: Promise<string> = new Promise((resolv, reject) => {
  setTimeout(() => {
    // resolve에서 문자를 반환하기 때문에 제네릭타입으로 string을 지정
    resolve("함수 실행");
  }, 2000);
});

promise.then((data) => {
  data.split(" ");
});
```

## 🔵 제네릭 함수

- `<T, U>` 제네릭 타입을 지정할 때 보통 T를 사용하며, 두 번째 제네릭 매개변수나 타입의 이름은 보통 U를 사용한다.

👾 두객체를 하나의 객체로 병합하는 함수

```js
function merge(obj1: object, obj2: object) {
  return Object.assign(obj1, obj2);
}

const mergedObj = merge({ name: "Max" }, { age: 25 });
mergedObj.name = "Mark"; // error
```

👉🏻 병합은 되지만 병합된 객체의 속성에 접근하면 error가 발생한다. <br>
👉🏻 `const mergedObj = merge({ name: "Max" }, { age: 25 }) as {name: string, age: number};`형변환을 통해 error를 해결할 수 있지만 제네릭을 통해 해결해 보자.

```js
function merge<T, U>(obj1: T, obj2: U) {
  // ts가 T와 U의 인터섹션을 반환한다고 추론
  return Object.assign(obj1, obj2);
}

const mergedObj = merge<{name: string}, {age: string}>({ name: "Max" }, { age: 25 });
// 제네릭도 구체적인 형식을 정할 수 있다.
mergedObj.name = "Mark";
```

👉🏻 `function merge<object, object>(obj1: object, obj2: object): object & object`은 미상의 두 객체의 인터섹션은 새로운 미상의 객체일뿐이므로 추가적인 타입 정보를 타입스크립트에게 넘겨 주지 않는다. <br>
👉🏻 **제네릭 타입을 사용하면 두 매개변수가 서로 다른 타입이 될수 있다고 TS에 알려줄 수 있다.**

## 🔵 타입 제약 조건

- 제네릭 타입을 기반으로 할 수 있는 타입에 대한 특정 제약 조건을 설정할 수 있다.
- extends를 사용한다.
- 유니온 타입등 다양한 타입을 extends할 수 있다.

👾

```js
function merge<T extends object, U extends object>(obj1: T, obj2: U) {
  return Object.assign(obj1, obj2);
}

const mergedObj = merge({ name: "Max" }, { age: 25 });
mergedObj.name = "Mark";
```

👉🏻 `T extends object` T타입이 어떤 구조를 가지든 객체여만 하면 된다.

## 🔵 다른 제네릭 함수

👾 legth속성을 지니는 값을 element인수로 호출하는 예제

```js
interface Lengthy {
  length: number;
}

function countAndDescribe<T extends Lengthy>(element: T): [T, string] {
  // 길이가 없는 요소를 전달 받았을 경우 출력할 내용
  let descriptionText = "Got no value.";

  if(element.length === 1){
    decsription = "Got 1 element";
  } else if(element.length > 1){
    decsription = "Got" + element.length + " elements";
  }
  return [element, descriptionText];
}

console.log(countAndDescribe("hello~!"));
```

## 🔵 keyof 제약조건

- 객체의 정확한 구조를 갖고자 할때 사용

```js
function extractAndConvert<T, U>(obj: T extends object, key: U extends keyof T) {
  // TS가 이 객체에 이 키가 있는지 보장할 수 없어 error발생
  return obj[key];
}
extractAndConvert({ name:"Max" }, "name");
```

👉🏻 `U extends keyof T` T객체에 모든 유형의 키(U)여야 한다고 설정

## 🔵 제네릭 클래스

```js
class DataStorage<T extends string | number | boolean > {
  private data: T[] = [];

  addItem(item: T){
    this.data.push(item);
  }
  removeItem(item: T){
    if(this.data.indexOf(item) === -1){
      return;
    }
    this.data.splice(this.data.indexOf(item), 1);
  }
  getItems(){
    return [...this.data];
  }
}

const textStorage = new DataStorage<string>();
textStorage.addItem(10); // error
```

## 🔵 제네릭 유틸리티 타입

- 내장 유틸리티 타입

### ● Partial

- 모든 속성이 선택적인 타입으로 바꾼다.

```js
interface CourseGoal {
  title: string;
  description: string;
  completeUntil: Date;
}

function createCourseGoal(
  title: string,
  description: string,
  data: Date
): CourseGoal{
  let courseGoal: Partial<CourseGoal> = {};
  courseGoal.title = title;
  courseGoal.description = description;
  courseGoal.completeUntil = date;
  return courseGoal as CourseGoal;
}
```

👉🏻 courseGoal이 CourseGoal이 되는 객체라는 것을 알려줌. 또한 선택적으로 속성을 가질 수 있다. <br>
👉🏻 문제는 courseGoal객체를 그냥 반환할 수 없고, CourseGoal 타입 지정을 해야 반환할 수 있다. 속성이 다 추가된 완전한 객체임을 알려줘야 한다.

### ● Readonly

- 읽기만 가능하게 한다.

```js
const names: Readonly<string[]> = ["Max", "Alie"];
names.push("Mark"); // error
```

## 제네릭와 유니온의 차이점

👾 유니온 타입 설정

```js
// 타입이 혼합된 배열 설정
const data (string | number | boolean)[] = [];
...
class DataStorage {
  // 해당 타입에 따른 배열 설정
  private data string[] | number[] | boolean[] = [];

  addItem(item: string | number | boolean){
    this.data.push(item); // error
  }
}
```

👾 제네릭 타입 설정

- 제네릭타입을 사용하게 되면 저장하려는 데이터의 타입을 하나 선택하고 그런 다음 해당 타입의 데이터만을 추가할 수 있음!!

```js
class DataStorage<T extends string | number | boolean > {
  private data T[]: [];

  addItem(item: T){
    this.data.push(item);
  }
}
```
