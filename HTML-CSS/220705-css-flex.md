- 22.07.05
- fastcampus Megabyte School : 프론트엔드 개발 취업 연계 과정 강의에서 복습한 내용을 간단하게 정리 하였습니다.

# flex

## ● flex container 속성

### 1. display: flex | inline-flex

- inline-flex: container기준으로 화면에 보이는 특성을 정의 함. 부모요소가 inline처럼 최소너비로 줄어듬.

### 2. flex-flow

### 3. flex-direction: row | row-reverse | column | column-reverse

: 주 축을 설정

### 4. flex-wrap: wrap | nowrap(df)

### 5. justify-content: flex-start(df) | center | flex-end | space-between | space-around

- space-between: 각 item 사이를 균등하게 정렬
- space-around: 각 item의 외부 여백을 균등하게 정렬
- space-evenly: 아이템들의 사이와 양 끝에 균일한 간격을 만들어 줌. IE와 엣지(Edge)에서는 지원되지 않음.

### 6. align-content: stretch(df) | flex-start | flex-end | center | space-between | space-around

🔥 교차 축의 여러 줄 정렬 방법!  
🔥 한 줄일 때 동작하지 않음!!!

- stretch, flex-start: 시작점으로 정렬

### 7. align-items: stretch(df) | flex-start | flex-end | center | baseline

🔥 교차 축의 한 줄 정렬 방법!

- stretch: 교차 축으로 늘림
- baseline: 각 줄의 문자 기준선에 정렬

## ● flex items 속성

### 1. order

: item의 순서를 지정

- 0: df, 순서 없음
- 숫자: 숫자가 작을 수록 먼저, 음수도 가능

### 2. flex-grow : 0(df)

: item의 증가 너비 비율
: 아이템이 얼마나 늘어나는 비율을 가질것 인지

- 0: df, 증가 비율 없음
- 숫자: 증가 비율

🔥 남은 공간을 가지고 비율대로 가져감.

### 3. flex-shrink: 1(df)

: item의 감소 너비 비율

- 1: df, container 너비에 따라 감소 비율 적용
- 숫자: 감소 비율

🔥 0을 적용하면 container가 줄어들 경우 item들의 넓이가 줄어들지 않음. 기본적으로 기본값이 1이기 때문에 container가 줄어들면 그에 맞게 item들도 줄어듬!!
🔥 1로 두면 flex-basis보다 작아질 수 있음

### 4. flex-basis: auto(df)

: item의 공간 배분 전 기본 너비를 지정함

- auto: df, 요소의 content너비
- 단위: px, em, rem등 단위로 지정

🔥 flex-basis: 100px 지정 시 item의 기본 너비가 100px이 됨.
🔥 원래 100px을 넘는 item도 100px로 맞춰짐. 안의 content(text)가 넓이보다 길어 다음 줄로 넘어가도록 하려면,CSS에 word-wrap: break-word;를 적용. 그렇지 않으면 content가 옆응로 삐죽 나옴.
🔥 flex-basis:0, flex-grow:숫자로 적용하면 content의 너비를 무시하고 해당 item들의 넓이가 숫자 비율대로 적용됨!!

### 5. flex

: flex-grow, flex-shrink, flex-basis를 한 번에 쓸 수 있는 축약형 속성

```css
.item {
  flex: 1;
  /* flex-grow: 1; flex-shrink: 1; flex-basis: 0%; */
  flex: 1 1 auto;
  /* flex-grow: 1; flex-shrink: 1; flex-basis: auto; */
  flex: 1 500px;
  /* flex-grow: 1; flex-shrink: 1; flex-basis: 500px; */
}
```

### 6. align-self: auto(df)

: 수직축으로 아이템 정렬
: align-items보다 우선권이 있음
