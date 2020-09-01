---
title: React - useReducer and dispatch context
date: '2019-12-12T14:12:27.000Z'
category: react
---

# useReducer와 context API함께 사용하기

useReducer와 useState는 state 상태를 관리하는 목적에서 매우 유사합니다. 다만 Setter함수에 state를 정의하는 방식이 useReducer가 더 명확하게 보입니다. useReducer와 useState의 차이점이 크게 와닿지 않을 수 있으나 useReducer와 createContext가 같이 사용될 경우 얘기가 달라지게 됩니다. useState와 useReducer의 코드를 다음과 같이 살펴보겠습니다.

## useState

App.js

```javascript
import React, {useState, useCallback} from 'react';
import CreateUser from "./CreateUser";

function App() {

  // 새로 입력받을 정보 초기화
  const [inputs, setInputs] = useState({
    username: '',
    email: ''
  });

  const onChange = useCallback((e) => {
    const {name, value} = e.target;

    // setInputs 함수에 inputs객체를 복사하여 inputs객체에 할당.
    setInputs({
      ...inputs,
      [name]: value
    });
  }, [inputs]);

  return (
    <>
      <CreateUser 
        username={username} 
        email={email} 
        onChange={onChange}
      />
    </>
  );
}

export default App;
```

onChange변수에 익명함수를 선언하여 Setter함수인 setInputs를 통해 state를 컨트롤 합니다. 다음으로 useReducer를 이어서 보겠습니다.

## useReducer

App.js

```javascript
import React, {useCallback, useReducer} from 'react';
import CreateUser from "./CreateUser";

const inputs = {
    username: '',
    email: ''
};

function reducer(state, action) {
  switch (action.type) {
      case 'CHANGE_INPUT':
      return {
          ...state,
          username: action.username,
          email: action.email
      };
    default:
      return state;
  }
}

function App() {
  const [state, dispatch] = useReducer(reducer, inputs);
  const {username, email} = state;
  const onChange = useCallback((e) => {
    const {name, value} = e.target;
    dispatch({
      type: 'CHANGE_INPUT',
        ...state,
        [name]: value
    });
  }, [state]);
  return (
    <>
      <CreateUser
        username={username}
        email={email}
        onChange={onChange}
      />
    </>
  );
}

export default App;
```

useReducer는 다음과 같은 특징을 가집니다.

* useState처럼 state를 업데이트를 할 수 있으며 객체를 통해 타입을 명시하여 액션을 정의할 수 있다. 또한 상태 업데이트 로직을 컴포넌트 밖으로 분리가 가능하다.
* dispatch로 넘겨받는 함수는\(ruducer\)는 두개의 파라미터를 받는다. 첫번째 파라미터는 기존 state의 값, 두번째 파라미터는 변경 될 값\(action\)을 받는다.
* reducer 함수에서 반환하는 형태는 컴포넌트가 가질 새로운 상태가 된다.
* 새로운 상태를 만들때는 항상 불변성을 유지시켜준다 - return {...spread}

useReducer를 사용하기 위해 준비단계가 다소 복잡해 보인다. 또한 useState와 다를게 없이 state를 업데이트하기 위한 기능일 뿐이다. 자금 createContext와 같이 살펴보자

## createContext

App.js

```javascript
import React, {useReducer, createContext} from 'react';
import CreateUser, {inputs} from "./CreateUser";

function reducer(state, action) {
  switch (action.type) {
    case 'CHANGE_INPUT':
      console.log(state);
      return {
        ...state,
        username: action.username,
        email: action.email
      };
    default:
      return state;
  }
}

export const UserDispatch = createContext(null);

function App() {
  const [state, dispatch] = useReducer(reducer, inputs);
  return (
    <UserDispatch.Provider value={{state, dispatch}}>
      <CreateUser/>
    </UserDispatch.Provider>
  );
}

export default App;
```

createUser.js

```javascript
import React, {useContext} from 'react';
import {UserDispatch} from './App'

function CreateUser() {
  const reducerObj = useContext(UserDispatch);
  const {state, dispatch} = reducerObj;
  const onChange = (e) => {
    const {name, value} = e.target;
    dispatch({
      type: 'CHANGE_INPUT',
      ...state,
      [name]: value
    });
  }
  return (
    <div>
      <input
        type="text"
        name="username"
        placeholder="아이디"
        onChange={onChange}
       />
      <input
        type="text"
        name="email"
        placeholder="이메일"
        onChange={onChange}
      />
      <button type="button" onClick={()=>{}}>추가</button>
    </div>
  );
}

export const inputs = {
  username: '',
  email: ''
};
export default CreateUser;
```

createContext는 다음과 같은 특징을 가집니다.

* context를 구독하고 있는 컴포넌트를 렌더링할 때 React는 트리 상위에서 가장 가까이 있는 짝이 맞는 Provider로부터 현재값을 읽는다.
* Provider를 제공하며 구독하는 컴포넌트들에게 context의 변화를 알린다.
* Provider하위에 또다른 Provider배치 가능
* Provider의 값이 바뀔 때마다 재렌더링 된다.

   
createContext를 사용하기 이전을 살펴보면 App.js 하나의 파일 안에서 상태값과 이벤트를 설정하여 컴포넌트에\(CreateUser\) 전달하는 방식으로 설정을 하였다면, createContext를 사용한 방법에서는 CreateUser.js 컴포넌트에서 값과 이벤트를 설정하여 부모 컴포넌트 App.js에 넘겨주는 방식으로 설정하였습니다. 즉, 부모 -&gt; 자식 방법에서 자식 -&gt; 부모의 형태의 로직으로 변경하였습니다. 이와같이 할 수 있었던 방법은 createContext에서 제공하는 Provider를 통해서 명시적으로 props를 넘겨주지 않아도 컴포넌트가 값을 공유할 수 있기 때문입니다.  
이와같은 방법은 코드를 직관적으로 개발할 수 있으며 컴포넌트의 특징인 부모 -&gt; 자식으로 값을 전달하는 형태의 개념을 깨트리고 직접적으로 컴포넌트에 접근할 때 유용하게 사용할 수 있는 방법입니다.

