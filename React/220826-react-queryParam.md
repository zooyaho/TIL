### ● 쿼리 매개변수 작업하기

- 쿼리 매개변수는 url에 **물음표와 함께 매개변수 쌍이 오는 경우**가 있는데 로드된 페이지에 데이터를 추가로 넣어 준다.
- todoId와 같은 일반 route 매개변수와 다른점은, 일반 매개변수는 필수로 작성해야하고 **쿼리 매개변수는 선택사항이다!**
- 물음표가 route매칭을 바꾸지 않음!! **쿼리 매개변수 데이터에 접속하여 로드된 페이지의 '행동'을 바꿈.**

#### ✔️ useLocation

- useHistory는 history객체에 접속하게 하고, history객체는 **url을 바꿀 수 있게 해줌.**
- useLocation은 location객체에 접속하게 하고, location객체엔 **최근 로드된 페이지와 URL에 대한 정보가 있음!**
- location객체엔 hash, key, pathname, search, state 키와 값이 있다.

#### 💡 예제

- 예를들어 todoList에서 정렬한다고 했을 때 todo를 id에 따라 오름차순이나 내림차순으로 정렬한 다음, 연도로 정렬할 경우
- **쿼리 매개변수를 이용해 정렬한 걸 저장하고, 쿼리 매개변수가 있는 링크를 공유할 수 있는데 다른 유저가 그 링크를 쓰면 정렬한 대로 자동 정렬이 된다.**
- 쿼리 매개변수가 url에 없으면 기본 설정대로 정렬이 된다.

1️⃣ 정렬 버튼 설정
👾 TodoList.js

```js
import {useHistory} from 'react-router-dom';
...
const history = useHistory();
const changeSortHandler = ()=>{
  // push로 해야 back버튼으로 변경하기 전의 URL사용 가능함.
  // sort 버튼 클릭 시 url 변경
  history.push('/todoes?sort=' + 'asc');
};
...
<div className={"sorting"}>
  <button onClick={changeSortHandler}>오름차순</button>
</div>
<ul>
...
```

2️⃣ 쿼리 매개 변수값을 읽어 내고 실행하면서 정렬과 버튼 이름을 바꾼다.

👾 TodoList.js

```js
import {useHistory, useLocation} from 'react-router-dom';
...
const history = useHistory();
const location = useLocation();

// Location객체의 search는 쿼리매개변수를 값으로 가지고 있다!
// URLSearchParams는 쿼리매개변수를 key로 추출하는 객체이다
// queryParams = { sort:"asc" }가 저장됨
const queryParams = new URLSearchParams(location.search);

// 오름차순이면 true가 할당됨
const isSortingAscending = queryParams.get('sort') === 'asc';

const changeSortHandler = ()=>{
  // 클릭할 때마다 TodoList컴포넌트는 재평가가 된다!!
  history.push('/todoes?sort=' + (isSortingAscending ? 'decs' : 'asc'));
};
...
<div className={"sorting"}>
  // 현재 오름차순일 경우 버튼 클릭 시 내림차순이 되어야함.
  <button onClick={changeSortHandler}>{isSortingAscending ? '내림차순' : '오름차순'}</button>
</div>
<ul>
...
```

3️⃣ 정렬 로직을 가지는 함수를 추가한다.

👾 TodoList.js

```js
const sortTodos = (todos, ascending) => {
  return quotes.sort((todoPrev, todoCur) => {
    if (ascending) {
      // 오름 차순
      return todoPrev.id > todoCur.id ? 1 : -1;
    } else {
      // 내림 차순
      return todoPrev.id < todoCur.id ? 1 : -1;
    }
  });
};

const TodoList = ()=>{
  ...
  // 오름차순이면 true가 할당됨
  const isSortingAscending = queryParams.get('sort') === 'asc';

  const sortedTodos = sortTodos(todos,isSortingAscending);
  ...
  return(
    ...
    <ul>
    {sortedTodos.map(()=>(
      <TodoItem />
    ))}
    </ul>
  )
}
```
