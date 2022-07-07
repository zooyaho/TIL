220707 - TIL

# 사용 목적에 따른 a, button, input button태그 알아보기

## 📎 button태그

```html
<button type="button"></button>
```

- 페이지 내에서 **인터페이스 조작을 위한 버튼**을 정의할 때 사용한다.
- type을 명시하지 않으면 어떤 브라우저는 Form Submit 기능을 실행하기 때문에 (submit목적이 아닌 버튼역할일 경우) 반드시 type 속성을 button으로 지정해주어야 한다.

## 📎 a태그

```html
<a href="/"></a>
```

- 페이지로 이동을 위해 링크를 생성할 때만 사용한다.  
  ex) 메뉴버튼에 따른 페이지 이동

## 📎 input button태그

```html
<input type="button" />
```

- 버튼 태그와 동일한 버튼 태그이지만, 일반적으로 **폼 데이터 제출 시, 사용**한다.  
  ex) 로그인 버튼

## 📎 div를 사용한 버튼

```html
<div class="toggleBtn" onClick="toggle"></div>
```

- 커스티마이징이 필요한 버튼은 div태그를 사용한다.  
  ex) svg를 이용한 토글버튼

> 참고 https://jsunnylab.tistory.com/38
