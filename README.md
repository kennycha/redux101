# Redux 101

## 0. Introduction

### # 0.4 Project Setup

- 생성

  ```bash
  $ npx create-react-app vanilla-redux
  ```

## 1. Pure Redux: Counter

### # 1.0 Vanilla Counter

``` html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vanilla Redux</title>
  </head>
  <body>
    <button id="add">Add</button>
    <span></span>
    <button id="minus">Minus</button>
  </body>
</html>

```

```js
const add = document.querySelector("#add");
const minus = document.querySelector("#minus");
const number = document.querySelector("span");

let count = 0;
number.innerText = count;

const updateText = () => {
  number.innerText = count;
};

const handleAdd = () => {
  count = count + 1;
  updateText();
};

const handleMinus = () => {
  count = count - 1;
  updateText();
};

add.addEventListener("click", handleAdd);
minus.addEventListener("click", handleMinus);
```

### # 1.1 Store and Reducer

- store

  - 애플리케이션 내에서 변하는 데이터인 `state`를 저장하는 곳

- reducer

  - `state`를 변경할 수 있는 함수
    - data modifier
    - return 값이 새로운 state가 된다
  - `state`와 `action`을 parameter로 받는다
  - `action`을 통해 reducer와 소통할 수 있다

- createStore()

  - store를 생성하는 함수
  - parameter로 reducer를 넣어준다

  ``` js
  import { createStore } from 'redux'
  
  const exampleReducer = (state = initialState, action) => {
      return state
  }
  
  const exampleStore = createStore(exampleReducer)
  ```

- getState()

  - store의 현재 state를 return하는 method

  ```js
  exampleStore.getState()
  ```

### # 1.2 Actions

- action
  - reducer의 두번째 parameter에 해당
  - reducer와 소통할 수 있게 해준다
    - 조건(type)에 따른 분기를 가능케 한다
  - action은 Object여야 하고, type을 포함해야 한다

- dispatch()

  - action을 보내는 함수
    - 정확히는 action을 가지고 reducer를 호출하는 함수

  ```js
  exampleStore.dispatch({ type: "EXAMPLE_TYPE"})
  
  // 아래와 같이 reducer 함수를 호출
  exampleReducer(currentState, { type: "EXAMPLE_TYPE"})
  ```

### # 1.3 Subscriptions

- subscribe()

  - state가 변할 때 함수를 호출하는 method

  ```js
  const on Change = () => {
    exampleElement.innerText = exampleStore.getState()
  }
  
  exampleStore.subscribe(onChange)
  ```

### # 1.4 Recap Refactor

- switch 문

  - instead of `if ~ else~ `
  - `switch~ case~ case~ default~`

  ```js
  const countModifier = (count = 0, action) => {
    switch (action.type) {
      case ADD:
        return count + 1;
      case MINUS:
        return count - 1;
      default:
        return count;
    }
  };
  ```

- action type을 변수화하여 사용

  - 오타 방지

  ```js
  const ADD = "ADD";
  const MINUS = "MINUS";
  
  const handleAdd = () => {
    countStore.dispatch({ type: ADD });
  };
  const handleMinus = () => {
    countStore.dispatch({ type: MINUS });
  };
  ```

- final code

  ```js
  import { createStore } from "redux";
  
  const add = document.querySelector("#add");
  const minus = document.querySelector("#minus");
  const number = document.querySelector("span");
  
  number.innerText = 0;
  
  const ADD = "ADD";
  const MINUS = "MINUS";
  
  const countModifier = (count = 0, action) => {
    switch (action.type) {
      case ADD:
        return count + 1;
      case MINUS:
        return count - 1;
      default:
        return count;
    }
  };
  
  const countStore = createStore(countModifier);
  
  const onChange = () => {
    number.innerText = countStore.getState();
  };
  
  countStore.subscribe(onChange);
  
  const handleAdd = () => {
    countStore.dispatch({ type: ADD });
  };
  const handleMinus = () => {
    countStore.dispatch({ type: MINUS });
  };
  
  add.addEventListener("click", handleAdd);
  minus.addEventListener("click", handleMinus);
  ```

## 2. Pure Redux: To Do List

### # 2.1 :x:State Mutation

- [redux - 3 principles](https://redux.ruanyifeng.com/introduction/ThreePrinciples.html)
  1. single source of truth
  2. state is read-only
     - you can modify state **only with dispatch**
  3. changes are made with pure functions
     - don't mutate the previous state
     - **return new state**
       - using [ES6 spread](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

### # 2.3 Delete To Do

- :x:splice()
  - mutate array
  - don't use this array method for the state
- filter()
  - create and return a new array, whose elements passed a test

- action creator function
  - action object를 return하는 함수

- final code

  ```js
  import { createStore } from "redux";
  
  const form = document.querySelector("form");
  const input = document.querySelector("input");
  const ul = document.querySelector("ul");
  
  // type 변수화
  const ADD_TODO = "ADD_TODO";
  const DELETE_TODO = "DELETE_TODO";
  
  // action creator func
  const addToDo = (text) => {
    return {
      type: ADD_TODO,
      text,
    };
  };
  const deleteToDo = (id) => {
    return {
      type: DELETE_TODO,
      id,
    };
  };
  
  // reducer (state modifier)
  const reducer = (state = [], action) => {
    switch (action.type) {
      case ADD_TODO:
        const newToDoObj = { text: action.text, id: Date.now() };
        return [newToDoObj, ...state];
      case DELETE_TODO:
        const cleaned = state.filter((toDo) => toDo.id !== action.id);
        return cleaned;
      default:
        return state;
    }
  };
  
  const store = createStore(reducer);
  
  // dispatch func
  const dispatchAddToDo = (text) => {
    store.dispatch(addToDo(text));
  };
  const dispatchDeleteToDo = (e) => {
    const id = parseInt(e.target.parentNode.id);
    store.dispatch(deleteToDo(id));
  };
  
  const paintToDos = () => {
    const toDos = store.getState();
    ul.innerHTML = "";
    toDos.forEach((toDo) => {
      const li = document.createElement("li");
      const btn = document.createElement("button");
      btn.innerText = "DEL";
      btn.addEventListener("click", dispatchDeleteToDo);
      li.id = toDo.id;
      li.innerText = toDo.text;
      li.appendChild(btn);
      ul.appendChild(li);
    });
  };
  
  // store에 변화가 생기면 paintToDos를 호출
  store.subscribe(paintToDos);
  
  const onSubmit = (e) => {
    e.preventDefault();
    const toDo = input.value;
    input.value = "";
    dispatchAddToDo(toDo);
  };
  
  form.addEventListener("submit", onSubmit);
  ```

## 3. React Redux

### # 3.1 Connecting the Store

- [react-redux](https://react-redux.js.org/introduction/quick-start)

  - redux를 react와 함께 사용하기 편하도록 해주는 패키지

- Provider

  - vanilla redux에서는 data 변화에 따른 re-rendering을 직접 구현
    - subscribe() method를 통해 변화를 감지하고
    - 변화가 생기면 paintToDo() 함수를 호출
  - react-redux의 `Provider` 요소를 사용
    - App에서 store에 접근할 수 있도록
    - `Provider` 사용시 prop으로 `store`를 넘겨줘야 한다

  ```jsx
  import React from "react";
  import ReactDOM from "react-dom";
  import App from "./components/App";
  import { Provider } from "react-redux";
  import store from "./store";
  
  ReactDOM.render(
    <Provider store={store}>
      <App />
    </Provider>,
    document.getElementById("root")
  );
  ```

### # 3.2 mapStateToProps

- connect()
  - 첫번째 parameter로 mapStateToProps 함수를,
  - 두번째 parameter로 mapDispatchToProps 함수를 받는다
  - `connect(mapStateToProps, mapDispatchToProps)(컴포넌트) ` 의 형식
    - mapStateToProps만 사용할 때는 `connect(mapStateToProps)(컴포넌트)`
    - mapDispatchToProps만 사용할 때는 `connect(null, mapDispatchToProps)(컴포넌트)` 의 

- mapStateToProps()

  - state를 component에 prop으로 전달하는 함수
    - store.getState() 를 통해 current state를 가져온 후, 원하는 내용을 props로 넘겨주는 것과 동일
  - return 에 해당하는 Object의 내용이 props 안에 들어가게 된다

  ```jsx
  import { connect } from "react-redux";
  
  // props 중 toDos를 받아와서 사용
  const Home = ({ toDos }) => {
    return (
      <div>{toDos}</div>
    );
  };
  
  // toDos를 props에 포함
  const mapStateToProps = (state) => {
    return { toDos: state };
  };
  
  // connect를 통해 적용
  export default connect(mapStateToProps)(Home);
  ```

### # 3.3 mapDispatchToProps

- reducer를 호출하는 dispatch 함수를 component에 prop으로 전달하는 함수

  - return 에 해당하는 Object의 내용이 props 안에 들어가게 된다

  ```jsx
  import { connect } from "react-redux";
  import { actionCreators } from "../store";
  
  const Home = ({ toDos, addToDo }) => {
    return (
        <ul>{JSON.stringify(toDos)}</ul>
    );
  };
  
  const mapStateToProps = (state) => {
    return { toDos: state };
  };
  const mapDispatchToProps = (dispatch) => {
    return {
      addToDo: (text) => dispatch(actionCreators.addToDo(text)),
    };
  };
  
  export default connect(mapStateToProps, mapDispatchToProps)(Home);
  ```

### # 3.4 Deleting To Do

- ownProps

  - mapStateToProps와 mapDispatchToProps의 두번째 parameter
  - 컴포넌트가 원래 가지고 있는 props

  ```jsx
  import { connect } from "react-redux";
  import { actionCreators } from "../store";
  
  const ToDo = ({ text, onBtnClick }) => {
    return (
      <li>
        {text} <button onClick={onBtnClick}>DEL</button>
      </li>
    );
  };
  
  const mapDispatchToProps = (dispatch, ownProps) => {
    return {
      onBtnClick: () => dispatch(actionCreators.deleteToDo(ownProps.id)),
    };
  };
  
  // state 변경 함수만 필요하기 때문
  export default connect(null, mapDispatchToProps)(ToDo);
  ```

- [useParams](https://reactrouter.com/web/api/Hooks)

  - URL params에 접근하는 hook
  - react-router-dom의 Router 내부에서 사용

  ```jsx
  import React from "react";
  import ReactDOM from "react-dom";
  import {
    BrowserRouter as Router,
    Route,
    useParams
  } from "react-router-dom";
  
  function BlogPost() {
    let { slug } = useParams();
    return <div>Now showing post {slug}</div>;
  }
  
  ReactDOM.render(
    <Router>
      <Route exact path="/" component={Home} />
      <Route path="/blog/:slug" component={BlogPost} />
    </Router>,
    node
  );
  ```