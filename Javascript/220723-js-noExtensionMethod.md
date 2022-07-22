220723 - TIL

> 참고 모던 자바스크립트 Deep Dive

# 객체 변경 금지

> - 기본적으로 프로퍼티 추가 금지를 의미
> - 동적 추가와 Object.defineProperty로 추가 방법이 금지된다.

|      구분      |          메서드          | 프로퍼티 추가 | 프로퍼티 삭제 | 프로퍼티 값 읽기 | 프로퍼티 값 쓰기 | 프로퍼티 어트리뷰트 재정의 |
| :------------: | :----------------------: | :-----------: | :-----------: | :--------------: | :--------------: | :------------------------: |
| 객체 확장 금지 | Object.preventExtensions |       X       |       O       |        O         |        O         |             O              |
|   객체 밀봉    | Object.preventExtensions |       X       |       X       |        O         |        O         |             X              |
|   객체 동결    | Object.preventExtensions |       X       |       X       |        O         |        X         |             X              |

```js
const obj = { a: "default" };

Object.seal(obj);

obj.b = "add";
console.log(obj); // { a: 'default' }

obj.a = "seal";
console.log(obj); // { a: 'seal' }

delete obj.a;
console.log(obj); // { a: 'seal' }

Object.freeze(obj);

obj.b = "add";
console.log(obj); // { a: 'seal' }

obj.a = "freeze";
console.log(obj); // { a: 'seal' }

delete obj.a;
console.log(obj); // { a: 'seal' }

/* change */
Object.seal(obj); // { a: 'seal' }

obj.a = "change seal";
console.log(obj);
```

👉🏻 seal()은 확장이 불가하고 **수정은 가능하며**, 삭제할 수 없음.  
👉🏻 freeze()은 확장이 불가하고 수정할 수 없으며, 삭제할 수 없음.

💡 [결론] 이 두 메서드의 공통점

1. 전달된 객체들은 확장할 수 없게 되어 새로운 속성을 추가할 수 없다.
2. 전달된 객체 내부의 모든 요소들은 구성 불가능하게 되어 삭제할 수 없다.
3. 스트릭트 모드에서 두 메서드를 사용한 객체 모두 obj.a = 500 같은 연산을 실행하면 에러가 발생한다.

🔥 높은단계로 봉인은 가능해도, 낮은단계로 해제는 불가능함!!

> freeze -> seal -> preventExtensions 는 불가능하여 freeze로 고정됩니다!!!
