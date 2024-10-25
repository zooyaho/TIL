# 자바스크립트 비동기 처리와 함수 개념 이해하기

> 자바스크립트는 비동기 처리를 효과적으로 수행할 수 있는 여러 가지 기능과 패턴을 제공합니다. 이 포스트에서는 콜백 함수, Promise, Async/Await, 일급 객체 및 커링(curring) 개념에 대해 자세히 살펴보겠습니다.

## 콜백 함수

콜백 함수는 다른 함수의 인자로 전달되어, 특정 작업이 끝난 후에 실행되는 함수를 의미합니다. 비동기 요청을 처리할 때, 작업의 실행 순서를 보장하기 위해 주로 사용됩니다. 그러나 콜백 함수가 중첩되면서 코드가 복잡해지는 현상을 콜백 지옥(Callback Hell)이라고 하며, 이로 인해 Promise와 같은 새로운 패턴이 필요하게 되었습니다.

### 콜백 함수 예시

```javascript
function fetchData(callback) {
  setTimeout(() => {
    const data = "Data received!";
    callback(data); // 작업이 끝난 후 콜백 함수 실행
  }, 1000);
}

fetchData((data) => {
  console.log(data); // "Data received!" 출력
});
```

## Promise

Promise는 비동기 작업의 결과를 나타내는 객체로, 현재 상태가 pending (대기 중), fulfilled (이행됨), 또는 rejected (거부됨) 중 하나입니다.

Promise는 다음과 같은 메서드를 제공합니다:

- then(): Promise가 이행되었을 때 실행할 함수를 지정합니다.
- catch(): Promise가 거부되었을 때 실행할 함수를 지정합니다.
- finally(): 이행 또는 거부 여부와 관계없이 마지막에 실행할 함수를 지정합니다.

### Promise 예시

```javascript
const fetchData = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const success = true; // 성공 여부
      if (success) {
        resolve("Data received!");
      } else {
        reject("Error occurred.");
      }
    }, 1000);
  });
};

fetchData()
  .then((data) => console.log(data)) // "Data received!" 출력
  .catch((error) => console.error(error));
```

## Promise.all()과 Promise.allSettled()

비동기 API 호출 시 워터폴 현상(API waterfall)을 방지하기 위해 Promise.all()과 Promise.allSettled() 메서드를 사용할 수 있습니다.

`워터폴 현상`이란 여러 비동기 작업이 순차적으로 진행되어, 총 소요 시간이 길어지는 현상을 말합니다.

### Promise.all()

> 모든 Promise가 이행될 때 이행되며, 하나라도 거부되면 전체가 거부됩니다. 반환값은 각 Promise의 결과 배열입니다.

```javascript
const getUser = () => Promise.resolve("User");
const getWeather = () => Promise.resolve("Sunny");
const getSpace = () => Promise.resolve("Space");
const getLeftEvent = () => Promise.resolve("Event");

async function fetchData() {
  const [userId, weather, space, leftEvent] = await Promise.all([
    getUser(),
    getWeather(),
    getSpace(),
    getLeftEvent(),
  ]);
  console.log(userId, weather, space, leftEvent); // "User Sunny Space Event" 출력
}

fetchData();
```

### Promise.allSettled()

> 모든 Promise가 처리될 때 이행되며, 하나라도 거부된 경우에도 진행할 수 있습니다. 각 Promise의 결과 `상태`를 포함한 배열을 반환합니다.

```javascript
const promises = [
  Promise.resolve("Success 1"),
  Promise.reject("Error 1"),
  Promise.resolve("Success 2"),
];

Promise.allSettled(promises).then((results) => {
  console.log(results);
  // [{ status: "fulfilled", value: "Success 1" }, { status: "rejected", reason: "Error 1" }, { status: "fulfilled", value: "Success 2" }]
});
```

## Async/Await

Async/Await는 Promise를 더 쉽게 사용할 수 있도록 `문법`을 제공하는 기능입니다. async 함수는 `항상 Promise 객체를 반환`하며, await 키워드는 async 함수 내에서만 사용할 수 있습니다.

Async/Await 예시

```javascript
async function fetchData() {
  try {
    const userId = await getUser();
    const weather = await getWeather();
    console.log(userId, weather);
  } catch (error) {
    console.error(error);
  }
}

fetchData();
```

## 일급 객체 (First-Class Object)

자바스크립트의 `함수`는 `일급 객체`입니다. 이는 **함수가 일반 객체처럼 취급될 수 있다는 것을 의미**합니다. 즉, 함수는 변수에 할당할 수 있고, 다른 함수의 인자로 전달될 수 있으며, 함수의 반환값으로 사용될 수 있습니다.

일급 객체의 조건

- 함수의 매개변수가 될 수 있어야 한다.
- 함수의 반환값이 될 수 있어야 한다.
- 명령문으로 할당할 수 있어야 한다.
- 동일하게 비교할 수 있어야 한다.

일급 객체 예시

```javascript
// 함수의 매개변수로 전달
function higherOrderFunction(callback) {
  callback();
}

higherOrderFunction(() => {
  console.log("This is a callback function!");
});

// 함수의 반환값으로 사용
function createGreeter(name) {
  return function () {
    console.log(`Hello, ${name}!`);
  };
}

const greetAlice = createGreeter("Alice");
greetAlice(); // "Hello, Alice!" 출력
```

## 커링 (Currying)

커링은 여러 개의 인자를 받는 함수를 각각 하나의 인자를 받는 함수로 변환하는 기술입니다. 이를 통해 함수의 재사용성을 높이고, 특정 인자에 대한 부분 적용을 쉽게 할 수 있습니다.

커링 예시

```javascript
const getInformation = (name) => (phone) => (address) =>
  `${name}, ${phone}, ${address}`;

const result = getInformation("데이지")("01012341234")("서울");
console.log(result); // "데이지, 01012341234, 서울" 출력
```
