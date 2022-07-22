220722 - TIL

- fastcampus Megabyte School : 프론트엔드 개발 취업 연계 과정 강의에서 복습한 내용을 간단하게 정리 하였습니다.

# Bootstrap

> - Sass 변수 및 믹스인, 반응형 그리드 시스템, 사전 구축된 광범위한 구성 요소 및 강력한 JavaScript 플러그인을 제공하는 세계에서 가장 인기 있는 프론트 엔드 오픈 소스 툴킷인 Bootstrap으로 반응형 모바일 우선 사이트를 빠르게 디자인하고 사용자 정의하십시오.

## ◾️ Bootstrap 연결

- index.html copy paste

👾#01 [CDN방식] css,js 연결

```html
<link
  href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css"
  rel="stylesheet"
  integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3"
  crossorigin="anonymous"
/>
<script
  src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"
  integrity="sha384-ka7Sk0Gln4gmtz2MlQnikT1wXgYsOg+OMhuP+IlRH9sENBO0LRn5q+8nbTov4+1p"
  crossorigin="anonymous"
></script>
```

👾#02 [CDN방식] Popper js, bootstrap js 각각 작성가능, Popper js를 먼저 작성해야함.

```html
<script
  src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.10.2/dist/umd/popper.min.js"
  integrity="sha384-7+zCNj/IqJ95wo16oMtfsKbZ9ccEh31eOz1HGyDuCQ6wgnyJNSYdrPa03rtR1zdB"
  crossorigin="anonymous"
></script>
<script
  src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.min.js"
  integrity="sha384-QJHtvGhmr9XOIpI6YVutG+2QOK9T+ZnN4kzFN1RtK3zEFEIsxhlmWl5/YESvpZ13"
  crossorigin="anonymous"
></script>
```

- bundle: 하나의 묶음. 외부에서 popper js패키지를 가지고와서 활용하는데, bundle이 popper js와 bootstrap을 묶어서 js파일을 제공함!

- Popper js: 팝업을 좀 더 쉽게 만들게 해주는 js 패키지

👾#03 [npm 설치]

> - bootstrap을 npm으로 설치 시 bootstrap에서 필요로하는 기능들만 가져와서 사용할 수 있으며, 제공하는 기능들을 커스텀해서 사용할 수 있음!

- 터미널: npm install bootstrap
- .scss: @import '../node_modules/bootstrap/scss/bootstrap.scss'
- .js: import bootstrap from "bootstrap/dist/js/bootstrap.bundle";

## ◾️ 테마 색상 커스터마이징

👾#01 색상 custom

> https://getbootstrap.com/docs/5.1/customize/color/

👉🏻 color menu 확인하기

```scss
$theme-colors: (
  "primary": $primary,
  "secondary": $secondary,
  "success": $success,
  "info": $info,
  "warning": $warning,
  "danger": $danger,
  "light": $light,
  "dark": $dark,
);
```

👉🏻 Sass map 형식으로 제공함

```scss
// Required, 필수적으로 import해야함
@import "../node_modules/bootstrap/scss/functions";

// Default variable overrides , 변경할거 오버라이드
$body-bg: #000;
$body-color: #111;

// Required, 필수적으로 import해야함
@import "../node_modules/bootstrap/scss/variables";
@import "../node_modules/bootstrap/scss/mixins";
@import "../node_modules/bootstrap/scss/root";

// Optional Bootstrap components here
@import "../node_modules/bootstrap/scss/reboot";
@import "../node_modules/bootstrap/scss/type";
// etc
```

## ◾️ 성능 최적화(트리쉐이킹)

> - 필요한 기능만 가져와서 사용할 수 있음.

- https://getbootstrap.com/docs/5.1/customize/optimize/
- 초기화가 필요한 컴포넌트도 있고, 필요한 컴포넌트도 있음.

1. npm @popperjs/core 다운
2. via javascript 부분이 있는 컴포넌트는 초기화가 필요함.
   ![](https://velog.velcdn.com/images/zooyaho/post/1c68c05c-d8ff-444b-aed3-6f350005dfd4/image.png)
