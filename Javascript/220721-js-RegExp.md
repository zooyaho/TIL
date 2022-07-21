220721 - TIL

- fastcampus Megabyte School : 프론트엔드 개발 취업 연계 과정 강의에서 복습한 내용을 간단하게 정리 하였습니다.

## 정규표현식

>

- 일정한 패턴을 가진 문자열의 집합을 표현하기 위해 사용하는 형식언어
- 문자열을 대상으로 패턴 매칭 기능을 제공함.

![](https://velog.velcdn.com/cloudflare/zooyaho/54dbc2b3-0f01-4153-abd4-c4f94b65d524/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-04-08%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%2012.47.42.png)

### 🔵 리터럴 방식으로 생성

> /pattern/[, flags]

```js
let regexp = /the/gi;
```

### 🔵 RegExp생성자 함수

> new RegExp(pattern,[, flags])

```js
const target = "Is this all there is";

const regexp = new regExp(/is/i); // ES6
//const regexp = new regExp(/is/,'i');
//const regexp = new regExp('is',i);

regexp.test(target); // true
```

### 🔵 RegExp 메서드

#### RegExp.prototype.exec()

> - 인수로 전달받은 문자열에 대해 정규 표현식의 패턴을 검색하여 매칭결과를 배열로 반환
> - 매칭 결과가 없는 경우 null을 반환.

#### RegExp.prototype.test()

> 매칭 결과를 불리언 값으로 반환

```js
const target = "Is this all there is";

regexp = /is/gi;
console.log(regexp.test(target)); // true
```

#### String.prototype.match()

>

- 대상 문자열과 인수로 전달받은 정규 표현식과의 매칭 결과를 배열로 반환
- exec메서드는 g플래그를 사용해도 첫번째 매칭결과만 반환하지만 match메서드는 **g플래그 지정 시 모든 매칭결과를 배열로 반환함.**

```js
const regexp = /is/g;
target.match(regexp); // ["is","is"]
```

#### String.prototype.replace()

>

- 첫번째 인자에 해당하는 문자가 두번째 인자로 변경됨.
- 원본이 수정되지 않음.

```js
const regexp = /is/gi;
console.log("교체", target.replace(regexp, "Rabbit"));
// 교체 Rabbit thRabbit all there Rabbit
console.log("원본", target);
// 원본 Is this all there is
```

### 🔵 플래그

>

- 하나 이상의 플래그를 동시에 설정할 수 있음.
- 어떠한 플래그를 사용하지 않은 경우 대소문자를 구별해서 패턴을 검색하고, 첫번째 매칭한 대상만 검색하고 종료함.

- `i(Ignore case)`: 대소문자를 구별하지 않고 패턴을 검색.
- `g(Global)`: 대소문자를 구별하고 모든 문자열을 전역 검색함.
- `m(Multi line)`: 문자열의 행이 바뀌더라도 패턴검색을 계속함. 문자열에서 줄바꿈이 되어어져 있는 줄마다 체크함!!

👾#01

```js
const str = `
010-1234-5678
thesecon@gmail.com
https://www.omdbapi.com/?apikey=7035c60c&s=frozen
The quick brown fox jumps over the lazy dog.
abbcccdddd
http://localhost:1234
`;
let regexp = /the/;
let regexpG = /the/g; // 일치하는 모든 문자를 배열로 출력
let regexpI = /the/i; // 대소문자 무시하고 체크함
let regexpGi = /the/gi;

console.log("match-플래그X", str.match(regexp));
console.log("match-g플래그", str.match(regexpG));
console.log("match-i플래그", str.match(regexpI));
console.log("match-gi플래그", str.match(regexpGi));
```

![](https://velog.velcdn.com/images/zooyaho/post/053b4ed8-9f78-4b09-9f80-6c781764a81d/image.png)

👾#02 - $ : 문장의 끝을 뜻함.

```js
/* 문장 끝에 .(온점)이 있는지 체크 */
console.log(str.match(/\.$/gi)); // 하나의 문자열 끝에 한번만 체크함
console.log(str.match(/\.$/gim)); // 문자열에서 줄바꿈이 되어어져 있는 줄마다 체크함!!
```

![](https://velog.velcdn.com/images/zooyaho/post/ef73ea8a-05e4-46e0-8e5c-5cd6ee863be1/image.png)

### 🔵 패턴

> 패턴은 /로 열고 닫으며 문자열의 따옴표는 생략함.

- `^` : [...]내의 ^은 **Not의 의미**, [...] 밖에서의 ^는 **문자열의 시작을 의미**
- `$` : **문자열의 마지막을 의미**
- `.`: 임의의 **문자 한 개를 의미**(공백 포함!)
- `+` : 앞선 패턴이 **최소 한번 이상 반복**되는 문자열을 의미, {1,}과 같은 의미
- `?` : 앞선 패턴이 **최대 한번(0포함) 이상 반복**되는 문자열을 의미. {0,1}과 같음, `ab?` : b가 없거나 b와 일치
- `|` : or과 같은 의미, `a|b` : a 또는 b와 일치
- `[abc]` : a또는 b또는 c
- `[a-z]` : a부터 z사이의 문자 구간에 일치(영어 소문자)
- `[A-Z]` : A부터 Z사이의 문자 구간에 일치(영어 소문자)
- `[0-9]` : 0부터 9사이의 문자 구간에 일치(숫자)
- `[가-힣]` : 가부터 힣사이의 문자 구간에 일치(한글)
- `\s` : 여러가지 **공백문자(스페이스, 탭 등)**를 의미함. [\t\r\n\v\f]와 같은 의미.
- `\b` : 63개 문자에 일치하지 않는 **문자 경계(Boundary).** ex) \b\w{2,3}\b -> 010-123-1234에서 ['010', '123']을 반환함.
- `\d` : **숫자(Disit)**에 일치, [0-9]와 같은 의미
- `\D` : **숫자가 아닌 문자**를 의미, [^0-9]
- `\w` : 63개 문자**(Wold, 대소영문52개 + 숫자10개 + \_)**에 일치. 즉 [A-Za-z0-9_]와 같은 의미
- `\W` : 알파벳, 숫자, 언더스코어가 아닌 문자를 의미, [^a-za-z0-9_]
- `{m,n}` : 반복검색, 최소 m번, 최대 n번 반복되는 문자열을 의미. 뒤에 공백이 있으면 정상적으로 작동하지 않음.
- `{n}` : n번 반복되는 문자열을 의미, {n,n}과 같은 의미.
- `{n,}` : 최소 n번 이상 반복되는 문자열을 의미.
- `(?=)` : 앞쪽 일치(Lookahead)
- `(?<=)` : 뒤쪽 일치(Lookabehind)

👾 #01 @를 기준으로 앞, 뒤 문자 반환하기

```js
const str = "thesecon@gmail.com";
console.log(str.match(/.{1,}(?=@)/g)); // ['thesecon']
console.log(str.match(/(?<=@).{1,}/g)); // ['gmail.com']
```

👾 #02 분해되지 않은 단어 레벨로 검색하기 위해서는 +를 함께 사용

```js
const target = "A AA B BB Aa Bb";
const regexp = /A+|B+/g;

target.match(regexp); // ["A", "AA", "B", "BB", "Aa", "Bb"]
```

👾 #03 **[]내의 문자는 or로 동작한다.** 뒤에 +를 사용하면 앞선 패턴을 한번 이상 반복한다.

```js
const regexp = /[AB]+/g;

target.match(regexp); // ["A", "AA", "B", "BB", "Aa", "Bb"]
```

👾 #04 **범위 지정하려면 []내에 -을 사용.**

```js
// 대문자만
let regexp = /[A-Z]/g;
target.match(regexp); // ["A", "AA", "B", "BB", "A", "B"]

//대소문자
regexp = /[A-Za-z]/g;
target.match(regexp); // ["A", "AA", "B", "BB", "Aa", "Bb"]
```

### 🔵 자주 사용하는 정규표현식

#### 📎 특정 단어로 시작하는지 검사

```js
const url = "https://example.com";
/^https?:\/\//.test(url); // true
/^(http|https):\/\//.test(url); // true
```

#### 📎 특정 단어로 끝나는지 검사

```js
const fileName = "index.html";
/html$/.test(fileName); // true
```

#### 📎 숫자로 이루어진 문자열인지 검사

```js
const target = '12345'
/^\d+$/.test(target); // true
```

#### 📎 하나 이상의 공백으로 시작하는지 검사

```js
const target = " hi";
console.log(/^[\s]+/.test(target)); // true
console.log(/^\s+/.test(target)); // true
```

#### 📎 아이디로 사용 가능한지 검사

```js
// 알파벳 대소문자 또는 숫자로 시작하고 끝나며 4~10자리인지 검사
const id = "abc123";
console.log(/^[A-Za-z0-9]{4,10}$/.test(id)); // true
```

#### 📎 핸드폰 번호 형식에 맞는지 검사

```js
const cellphone = "010-1234-5678";
/^\d{3}-\d{4}-\d{4}$/.target(cellphone); // true
```

#### 📎 특수 문자 포함 여부 검사

```js
const target = "abc#123";
/[^A-Za-z0-9]/gi.test(target); // true

// 특수 문자 제거
target.replace(/[^A-Za-z0-9]/gi, ""); // 'abc123'
```
