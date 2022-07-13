220713 - TIL

> 참고) 모던 자바스크립트 Deep Dive

# JS 용어 정리

## 리터럴

> 리터럴은 사람이 이해할 수 있는 문자 또는 약속된 기호를 사용해 값을 생성하는 표기법을 말한다.

## 표현식

> 표현식은 값으로 평가될 수 있는 문이다. 즉, 표현식이 평가되면 새로운 값을 생성하거나 기존 값을 참조한다.

- 리터럴, 식별자(변수, 함수등의 이름), 연산자, 함수 호출 등의 조합으로 이뤄질 수있다.
- 변수 선언문은 값으로 평가될 수 없으므로 표현식이 아니다.
- 변수에 할당을 해보면 표현식인 문인지 구별할 수 있다.
- 크롬 개발자 도구에서는 표현식이 아닌 문을 실행하면 언제나 undefined를 출력한다.
- 리터럴은 값으로 평가된다. 따라서 리터럴도 표현식이다.

## 피연산자

> 피연산자는 값으로 평가될 수 있는 표현식이어야 한다.

## 암묵적 타입변환

> 자바스크립트엔진은 가급적 에러를 발생시키지 않도록 암묵적 타입 변환을 통해 표현식을 평가합니다.

### ● 문자열 타입으로 변환

- +연산자는 피연산자 중 하나 이상이 문자열이면 문자열 연결 연산자로 동작한다.

```js
  0 + '' // '0'
  -1 + '' // '-1'
  NaN + '' // 'NaN'
  false + '' // 'false'
  null + '' // 'null'
  ({}) + '' // '[object object]'
  [] + '' // ''
  [10,20] + '' // '10,20'
```

### ● 숫자 타입으로 변환

- 단항연산자(+,-)는 피연산자가 숫자 타입의 값이 아니면 숫자타입의 값으로 암묵적 타입 변환을 수행하는데, 빈 문자열(''), 빈 배열([]), null, false는 0,-0으로, true는 1,-1로 변환된다. 객체와 빈 배열이 아닌 배열, undefined는 변환되지 않아 NaN이 된다.

```js
+[] + // 0
  {} + // NaN
  null - // NaN
  false - // -0
  ""[10]; // -0 // NaN
undefined; // NaN
```

### ● 불리언 타입으로 변환

> 자바스크립트 엔진은 불리언 타입이 아닌 값을 Truthy 값 또는 Falthy 값으로 구분한다.

#### Falthy

- false
- undefined
- null
- 0, -0
- NaN
- ''(빈문자열)

#### Truthy

- Falthy 값 외의 모든 값

## 명시적 타입 변환

> 표준 빌트인 생성자 함수를 new연산자 없이 호출하는 방법과 빌트인 메서드를 사용하는 방법, 암묵적 타입변환을 이용한다.

### ● 문자열 타입으로 변환

- String 생성자 함수를 new연산자 없이 호출하는 방법 `String(NaN); // 'NaN'`
- Object.prototype.toString 메서드를 사용하는 방법 `(true).toString(); 'true'`
- 문자열 연결 연산자를 이용하는 방법 `[] + '' // ''`

### ● 숫자 타입으로 변환

- Number 생성자 함수를 new 연산자 없이 호출하는 방법 `Number(true) // 1`
- parseInt, parseFloat 함수를 사용하는 방법(문자열만 숫자 타입으로 변환 가능) `parseInt('1.6') // 1`
- +단항 산술 연산자를 이용하는 방법 `+[] // NaN`
- \*산술 연산자를 이용하는 방법 ` false * 1 // 0`

### ● 불리언 타입으로 변환

- Boolean 생성자 함수 new연산자 없이 호출하는 방법 `Boolean(Infinity) // true`
- ! 부정 논리 연사자를 두번 사용하는 방법 `!!{} // true`

## 단축 평가

> 표현식을 평가하는 도중에 평가 결과가 확정된 경우 나머지 평가 과정을 생략하는 것을 말한다.

### ● 논리 연산자를 사용한 단축 평가

- 논리합(||) 또는 논리곱(&&) 연산자 표현식의 평가 결과는 <b>불리언 값이 아닐 수도 있다.</b>
- 논리합(||) 또는 논리곱(&&) 연산자 표현식은 <b>언제나 2개의 피연산자 중 어느 한쪽으로 평가된다.</b>
- 단축 평가를 사용하면 if문을 대체할 수 있다.

```js
let done = true;
let msg = done && "완료";
console.log(msg); // '완료'
```

```js
true || anything; // true
false || anything; // anything
true && anything; // anything
false && anything; // false
```

💡 논리곱(&&)은 첫번째 Falthy값을 반환, 모두 Truthy일 경우 마지막 피연산자를 반환!!  
💡 논리합(||)은 첫번째 Truthy값을 반환, 모두 Falthy일 경우 마지막 피연산자를 반환!!

```js
"cat" || true; //'cat'
true || "cat"; // true
true && "cat"; // 'cat'
false && "cat"; // false
false && null; // false
```

#### 함수 매개변수에 기본값을 설정할 때

> 함수를 호출할 때 인수를 전달하지 않으면 매개변수에는 undefined가 할당된다. 이때 단축평가를 사용해 매개변수의 기본값을 설정하면 undefined로 인해 발생할 수 있는 에러를 방지할 수 있다.

```js
function getStringLength(str) {
  str = str || "";
  return str.length;
}
getStringLength(); // 0
getStringLength("hi"); // 2

// 매개변수에 기본값 설정
function getStringLength(str = "") {
  return str.length;
}
```

### ● 옵셔널 체이닝 연산자(?.)

- 좌항의 피연산자가 null 또는 undefined인 경우 <b>undefined를 반환</b>하고, 그렇지 않으면 우항의 프로퍼티 참조를 이어간다.
- 객체를 가리키기를 기대하는 변수가 null또는 undefined가 아닌지 확인하고 프로퍼티를 참조할 때 유용함.

```js
let str = "";
let length = str?.length;
console.log(length); // 0

length = str && str.length;
console.log(length); // ''
```

### ● null병합 연산자(??)

- 좌항의 피연산자가 null 또는 undefined인 경우 우항의 피연산자를 반환하고, 그렇지 않으면 좌항의 피연산자를 반환한다.
- 변수에 기본값을 설정할 때 유용하다.

```js
let foo = null ?? "default string";
console.log(foo); // 'default string'

// ''가 유효한 값일 경우 ||보다는 ??사용이 적합함.
foo = "" ?? "default string";
console.log(foo); // ''

foo = "" || "default string";
console.log(foo); // 'default string'
```
