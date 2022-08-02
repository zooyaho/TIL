220801 - TIL

- fastcampus Megabyte School : 프론트엔드 개발 취업 연계 과정 강의에서 복습한 내용을 간단하게 정리 하였습니다.

# [리액트 1회차]

## 1. render() 메서드

```js
root.render(<App />);
root.render(App());
```

- 같은 react Element를 반환함
- App()을 JSX문법으로 `<App />` 표현하는 것

![](https://velog.velcdn.com/images/zooyaho/post/8ba30c13-9b4b-4dff-8ab7-dc2a97f4bf22/image.png)

1. render()메서드는 받은 인자를 dom element로 그려줌.
2. render()메서드는 인자로 React.ReactNode타입이 들어감.
3. JSX에서 { }내에 자바스크립트 영역!!!!

## 2. ReactNode타입

: boolean, null, undefined는 ReactNode이지만 렌더링이 되지 않고 무시된다!!
ex) `<div>{false}</div>` // false가 출력되지 않고 빈 div가 그려지게 됨.

```
💡 ReactNode타입으로는 ReactElement, string, number, boolean, null, undefined 등이 있음.
```

![](https://velog.velcdn.com/images/zooyaho/post/37c8cd90-99ff-48fd-83e0-03d866e2b6ee/image.png)

## 3. props응용

```js
<MyComponent>hello</MyComponent>
```

: 컴포넌트 안에 있는 텍스트노드는 children프롭에 자동으로 넘어간다
: {매개변수.children} // hello 출력

## 4. { }에 배열

: { }에 배열을 담고 있는 변수를 작성하면 배열 전체를 하나의 문자로 인식하여 반환을 함.  
ex) `arr = [1,2,3]; -> {arr}` // 123 반환

![](https://velog.velcdn.com/images/zooyaho/post/e95fee05-2511-4764-943e-bea37735c319/image.png)

## 5. 리액트에서 랜더링

: 리액트에서 랜더링은 ReactElement를 Dom Element로 변환하는 과정인데 이 과정에서 기존에 있는 Dom El과 비교하여 변경된 부분만 바꿔줌!! -> 성능적 이점!

## 6. key프롭스의 역할

: key를 통해 기존 트리와 이후 트리의 자식들이 일치하는지 확인.
: 값 설정은 유니크하면서 변하지 않는 값으로 해야 함.
