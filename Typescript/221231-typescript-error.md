## 에러 처리법

- 🔥 catch에서 error의 타입은 `unknown`으로 지정, 타입을 지정(as사용)하고 사용해야한다!!

👾 TS에 정의된 Error타입

```js
interface Error {
  name: string;
  message: string;
  stack?: string;
  response?: {
    data: any,
  };
}
```

- axios 라이브러리를 사용하게 되면 response속성 타입을 추가하게 된다.

```js
interfacs Axios{
  get(): void;
}
interface CustomError extends Error{
  response?: {
    data: any,
  };
}
// interface와 class의 차이 밑에서 조건문으로 error의 타입을 instanceof를 사용하여 지정할 때 class로 정의해야 instanceof를 사용할 수 있다.
// js로 변활할때 interface는 사라지고 class는 남아있기 때문
class CustomError extends Error{
  response?: {
    data: any,
  };
}
declare const axios: Axios;
```

```js
(async () => {
  try{
    await axios.get();
  } catch (err: unknown){
    console.error((err as CustomError).reponse?.data);
    err.response?.data; // ❌ error
    // 위에서 타입 지정한 것은 일회성으로 간주하여 error를 발생한다.
    // 변수에 저장한 후 사용하면 as의 중복사용을 없애준다
  }
})();
```

```js
(async () => {
  try{
    await axios.get();
  } catch (err: unknown){
    // as는 타입이 unknown일 때 사용해야한다.
    const customError = err as CustomError;
    customError.response?.data;
    // 하지만 이렇게 사용하면 또 Error가 발생함~~
  }
})();
```

```js
(async () => {
  try{
    await axios.get();
  } catch (err: unknown){
    if(err instanceof CustomError){
      // 이렇게 조건문으로 감싸줘야 error발생하지 않음
      // 발생한 err가 CustomError가 아닐 수 있기 때문!
      console.error(err.reponse?.data);
      err.response?.data;
      // 이때는 변수에 정의해서 사용하지 않아도됨!
      // if문에서 타입가드를 하여 TS가 타입추론을 하기 때문!
    }
  }
})();
```
