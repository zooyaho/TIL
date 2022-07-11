220711 - TIL

- fastcampus Megabyte School : 프론트엔드 개발 취업 연계 과정 강의에서 복습한 내용을 간단하게 정리 하였습니다.

# DOM API

> - Document Object Model, Application Progamming Interface
> - 자바스크립트로 html을 조작하는 명령을 뜻함

- `querySelector` : HTML요소 검색/찾기
- `querySelectorAll` : HTML요소 모두 검색/찾기, **유사배열을 반환**
- `classList.add('')`: class 추가
- `classList.contains('')` : boolean반환
- `classList.remove('')` : class삭제
- `forEach(function( boxEl, index ){})`: 찾은 요소를 반복해서 함수 실행
  - boxEl: 반복 중인 요소
  - index: 반복 중인 번호
- `textContent`: 요소의 text부분

```js
console.log(boxEl.textContent); // Getter, 값을 얻는 용도
boxEl.textContent = "hello"; // Setter, 값을 지정하는 용도
```

> 참고 06. DOM API (1) - Ch 9. JS 선행

# 메소드 체이닝(Method Chaining)

> 메소드가 체인 형식으로 연결 되어있는걸 뜻함

- split: 문자를 인수 기준으로 쪼개서 배열로 반환.
- reverse: 배열을 뒤집기.
- join: 배열을 인수 기준으로 문자로 병합해 반환.

```js
const a = "Hello~";
const b = a.split("").reverse().join(""); // 메소드 체이닝
```

> 참고 08. 메소드 체이닝 - Ch 9. JS 선행
