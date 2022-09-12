# 데코레이터(Decorators)

## 🔔 설정

- tsconfig.json에서 `"experimentalDecorators": true` 활성화

## 데코레이터란

- 데코레이터는 실체화되기 전 클래스가 정의만 돼도 실행된다.
- 자바스크립트에서 클래스 및 컨스트럭터의 함수 정의만 입력되면 데코레이터가 돌아간다.
- 컨스트럭터 함수가 대상을 실체화하지 않았을 때도 데코레이터가 돌아간다.
- @ : 코딩에서 읽히거나 찾게 되는 특별한 식별자 상징이다.
- 데코레이터를 어디에 쓸지에 따라 인수의 개수가 정해진다.
- 보통 class에서 많이 사용한다.

👾 예시

```js
function Logger(constructor: Function) {
  console.log("Logging....");
  console.log(constructor);
}

@Logger
class Person {
  name = "Max";
  constructor() {
    console.log("Creating person object");
  }
}

const user = new Person();
```

![](https://velog.velcdn.com/images/zooyaho/post/00c5465c-21df-4915-a9da-741c75e7ced9/image.png)

### 데코레이터 팩토리

- 어떤 대상에 데코레이터를 할당할 때 설정할 수 있도록 해준다.
- 데코레이터를 내부에 설정하여 사용할 때보다 많은 영향력을 줄 수 있다.

👾 예시

```js
function Logger(logString: string) {
  return function (constructor: Function) {
    console.log(logString);
    console.log(constructor);
  };
}

@Logger("Logging - Person")
class Person {
  name = "Max";
  constructor() {
    console.log("Creating person object");
  }
}

const user = new Person();
```
