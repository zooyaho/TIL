220719 - TIL

- fastcampus Megabyte School : 프론트엔드 개발 취업 연계 과정 강의에서 복습한 내용을 간단하게 정리 하였습니다.

## 1. 문자(String)

### ● String.prototype

- `indexOf()` : String객체에서 주어진 값과 일치하는 첫번째 인덱스를 반환, 일치하는 값이 없으면 -1반환
- `slice()` : 문자열의 일부를 추출하면서 새로운 문자열을 반환. slice(여기서부터, 직전까지)
- `splice()` : 문자열의 일부를 추출하면서 새로운 문자열을 반환. slice(여기이후부터, 직전까지)
- `replace()` : (str1,str2), str1을 str2로 교체함
- `match()` : 특정한 문자데이터에서 정규표현식 조건에 맞는 배열을 반환함
- `trim()` : 특정한 문자데이터의 앞뒤로 공백을 없애줌

> 참고 01. 문자 - Ch 1. JS 데이터

## 2. 수자와 수학(Number)

### ● Number.prototype

- `toFixed()` : toFixed(소수점자리수), 실수로 만듬, **문자(string)로 반환함!!!!**
- `parseInt()` : parseInt(문자), 정수로 변환
- `parseFloat()` : parseFloat(문자), 실수를 유지하여 반환

### ● Math - 내장 객체, Number자료형만 지원함.

- `Math.abs()` : 주어진 숫자의 절대값을 반환
- `Math.min()`
- `Math.max()`
- `Math.ceil()` : 기본적으로 정수 올림 처리
- `Math.floor()` : 기본적으로 정수 내림 처리
- `Math.round()` : 기본적으로 정수 반올림 처리
- `Math.random()` : 0이하의 실수들이 랜덤으로 반환

## 3. 배열

### ● Array.prototype

- `length` : [].length
- `concat()` : arr1.concat(arr2), 두개의 배열이 합쳐진 새로운 배열 반환, 원본배열 수정X
- 🔥`forEach()` : 배열의 length만큼 반복하여 실행 -> 반환, forEach((item, index, arr)=>{})
- 🔥`map()` :배열의 length만큼 반복후에 새로운 배열을 반환, return값들의 배열을 반환하는 것임!! map((item, index, arr)=>{ return ;})
- `filter()` : return 조건에 true인 item의 값들이 새로운 배열로 반환됨.
  filter((item, index, arr)=>{return 조건;})
- `find()` : 주어진 판별 함수를 만족하는 첫 번째 요소의 값을 반환. 그런 요소가 없다면 undefined를 반환, find((item, index, arr)=>{ return 조건; })
- `findIndex()` : 주어진 판별 함수를 만족하는 첫 번째 요소의 인덱스를 반환, findIndex((item, index, arr)=>{ return 조건; })
- `includes()` : includes(특정한 데이터), 배열에 특정한 데이터가 포함되어 있는지, 불린 값을 반환.

⭐️ 여기부터 메서드들은 원본을 수정시킴!!

- `push()` : 배열의 뒷부분에 요소 추가 후 변경된 배열 반환.
- `unshift()` : 배열의 앞부분에 요소 추가 후 변경된 배열 반환.
- `reverse()` : 배열의 순서를 거꾸로 뒤집음.
- 🔥`splice()` :
  - 배열의 특정한 요소들을 지움. 삭제된 요소들의 배열을 반환함. slice(여기부터, 삭제개수)
  - slice(삭제후추가할index, 삭제개수 , 추가할요소데이터), item을 지우지 않고 첫번째 인수에 데이터를 추가하여 반환.
  - ex) 삭제 - splice(1,2) / 끼워넣기 - splice(1,0, 999) / 삭제 후 끼워넣기 - splice(1,2,999)

> 참고 03. 배열(1) - Ch 1. JS 데이터, 04. 배열(2) - Ch 1. JS 데이터

## 4. 객체

### ● Object - 정적 메서드(static)

> 객체 리터럴에 직접적으로 사용할 수 없음!!

- `Object.assign()` :
  - Object.assign(target, source): target객체에 source를 복사해서 복사한 target객체를 반환
  - target객체 원본 수정됨!
  - 출처객체인 source객체의 개수는 한개 이상임!!!
  - 이때 **반환된 객체는 target객체와 같은 메모리 주소를 공유함!!!**
  - **🔥깊은복사까지 해버림**
  - Object.assign({}, target, source): 빈객체에 여러 객체를 복사한 객체를 반환함~!
- `Object.keys()` : 객체의 key들을 요소로 갖는 배열을 반환함!

> 참고 05. 객체 - Ch 1. JS 데이터
