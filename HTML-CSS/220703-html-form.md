220703 - TIL

# form

## form태그의 속성

- method: 사용자가 입력한 내용을 서버 쪽 프로그램으로 어떻게 넘겨줄 것인지 지정함.
  - get: 주소 표시줄에 사용자가 입력한 내용이 그대로 드러나는 단점.
  - post: 입력한 내용의 길이에 제한받지 않고 사용자가 입력한 내용도 드러나지 않음.
- name: 자바스크립트로 폼을 제어할 때 사용할 폼의 이름을 지정
- action: `<form>`태그 안의 내용을 처리해 줄 서버 프로그램을 지정.
- target: action 속성에서 지정한 스크립트 파일을 현재 창이 아닌 다른 위치에서 열도록 함.

## `<fieldset>, <legend>`

- `<fieldset>`: 하나의 폼 안에서 여러 구역을 나누어 표시할
  때 사용

```html
<fieldset>
  <legend>상품 선택</legend>
</fieldset>
```

## `<label>`

: 레이블이란 입력란 가까이에 아이디나 비밀번호처럼 붙여 놓은 텍스트를 말함.

```html
// 01.
<label>아이디 : <input type="text" /></label>
// 02.
<label for="user-id">아이디(6자 이상)</label>
<input type="text" id="user-id" />
```

## `<input type="text">, <input type="password">`

속성

- size: 필드의 길이 지정,화면에 몇 글자가 보이도록 할 것인지 지정.
- value: 화면에 표시될 때 텍스트 필드 부분에 보여 주는 내용.

## input태그의 주요 속성

- autofocus: 페이지를 불러오자마자 폼에서 원하는 요소에 마우스 포인터를 표실할 수 있음.
- placeholder: 힌트 내용을 표시
- readonly: 사용자가 입력은 못하고 읽게만 하는 읽기 전용 필드를 만듬.
- required: 필수로 입력해야하는 필드를 지정

## 드롭다운 목록을 만들어 주는 `<select>`, `<option>`

- `<select>`
  - size: 화면에 표시할 드롭다운 항목의 개수 지정
  - multiple: 드롭다운 목록에서 둘 이상의 항목을 선택할 때 사용.
- `<option>` - value: 해당 항목을 선택할 때 서버로 넘겨줄 값을 지정 - selected: 드롭다운 메뉴를 삽입할 때 기본적으로 선택해서 보여 줄 항목을 지정
  ex

```html
<label for="animal">좋아하는 동물</label>
<select id="animal">
  <option value="rabbit">토끼</option>
  <option value="monkey" selected>원숭이</option>
  <option value="elephant">코끼리</option>
</select>
```
