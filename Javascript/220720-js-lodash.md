220720 - TIL

- fastcampus Megabyte School : 프론트엔드 개발 취업 연계 과정 강의에서 복습한 내용을 간단하게 정리 하였습니다.

## 1. 가져오기, 내보내기

### 🔵 가져오기(import)

>

- Named export: import시 가져올 함수에 {}를 감싸야 함
- Default로 지정된 기본통로로 나오는 데이터는 {} 감싸지 않음
- import로 가져온 함수에 별칭 지정하기 : as

👾#1

```js
import _ from "lodash"; // 'node_modules' 폴더에서 가져옴
import random from "./getType"; // getType.js

import { getType } from "./getType";
import { getType, random } from "./getType";
// 두가지 이상의 함수를 가져올 경우 , 사용

/* 별칭 지정 */
import { random, user as zooyaho } from "./gerRandom";

/* 모든 것을 가져오기 : import * */
import * as R from "./getRandom";
```

### 🔵 내보내기(export)

>

- Default export : 함수에 이름을 지정할 필요 없음, default 한번만 사용 가능

👾#2

```js
export getType(data){...}
export default random(data){...} // random 생략 가능
...
import {getType} from './getType'

```

## 2. Lodash 사용법

- `npm i lodash`
- `import _ from 'lodash'`

### 🔵 \_.uniq()

> - 중복된 데이터를 삭제 해주는 메소드

- 단순한 배열 값의 고유 값을 얻어야 한다면 uniq([배열데이터]) 메소드를 사용

👾#1

```js
_.uniq([2, 1, 2]);
// => [2, 1]
```

### 🔵 \_.uniqBy()

>

- \_.uniqBy([배열데이터], 기준값): 컬렉션 데이터를 특정 값을 기준으로 고유의 값(unique)만 가져올 수 있음.

👾#2

```js
const userA = [{id='1', name='park'},{id='2', name= 'mark'}]
const userB = [{id='1', name='park'},{id='3', name= 'jessi'}]

const userC = userA.concat(userB);
const newArray = _.uniqBy(userC, 'id');
console.log(newArray)
// {id='1', name='park'} {id='2', name= 'mark'} {id='3', name= 'jessi'}
```

### 🔵 \_.unionBy()

> - \_.unionBy(배열데이터A, 배열데이터B, 중복 구분할 고유의 속성명)

- 합치면 중복된 데이터가 생성되는 두개의 배열을 합치기 전에 사용 -> 중복이 제거되어 합쳐짐
- concat() 후 \_.uniqBy()하는 것과 같음.

👾#3

```js
const userA = [{id='1', name='park'},{id='2', name= 'mark'}]
const userB = [{id='1', name='park'},{id='3', name= 'jessi'}]

const newArray = _.unionBy(userA,userC, 'id');
console.log(newArray)
// {id='1', name='park'} {id='2', name= 'mark'} {id='3', name= 'jessi'}
```

👉🏻 데이터 고유화 처리 시 uniqBy()는 배열데이터가 하나일때 unionBy() 배열데이터가 여러개일 때 사용함~!!

### 🔵 \_.find()

> - \_.find([배열데이터], 찾을 데이터)
>   : 찾은 결과를 반환함!

```js
const users = [
{id="1", name="jiwoo"},
{id="2", name="zooyaho"}
]
const foundUser = _.find(users, {name="zooyaho"})
console.log(founderUser)
// {id="2", name="zooyaho"}

```

### 🔵 \_.findIndex()

> - \_.findIndex(객체로 배열데이터를 가진 변수, 찾을 데이터)
>   : 해당하는 index번호를 반환함!

```js
const foundUserIndex = _.find(users, {name="zooyaho"})
console.log(founderUser) // 1
```

### 🔵 \_.remove()

> - \_.remove(객체로 배열데이터를 가진 변수, 삭제할 데이터)
>   : 원본데이터 변경됨!

```js
_.remove(users, {name="zooyaho"})
console.log(users)
// (1) [{id="1", name="jiwoo"}]
```

👉🏻 users에 있는 item이 삭제됨!!
