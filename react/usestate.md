---
title: React - useState
date: '2019-12-08T20:13:31.000Z'
category: react
description: >-
  useState는 Hooks의 기술중 하나이며 state값을 바꿀 수 있는 기능을 가지고 있습니다. 함수형 컴포넌트에서 useState는
  사용할 수 없었지만 리액트 16.8버전 이후 사용이 가능합니다.
---

# useState

useState는 Hooks의 기술중 하나이며 state값을 바꿀 수 있는 기능을 가지고 있습니다. 함수형 컴포넌트에서 useState는 사용할 수 없었지만 리액트 16.8버전 이후 사용이 가능합니다.  
useState에서 state를 다루는 방법은 두가지 방법이 있습니다. 첫번째 단일 값을 설정할 때, 두번째 여러개의 값을 설정할 때 이며 코드는 다음과 같이 작성할 수 있습니다.

단일 값 \(String\)

```text
    // state
    const [user, setUser] = useState('kevin');
```

여러개의 값\(Object or Array\)

```text
    // state
    const arr = [
        {
            id: 1,
            username: 'Kevin',
            email: 'Kevin@gmail.com',
            active: false
        }, {
            id: 2,
            username: 'Alice',
            email: 'Alice@gmail.com',
            active: false
        }, {
            id: 3,
            username: 'Judith',
            email: 'Judith@gmail.com',
            active: false
        }
    ];
    const [users, setUsers] = useState(arr);
```

useState는 기본적으로 두개의 배열을 반환합니다. 첫번째 Index에는 설정값 변수, 두번째 Index에는 첫번째 Index를 변경할 Setter 함수를 반환합니다. UseState 다룰때 값의 형태는 자유이며 숫자, 문자열, 객체, 배열이 올 수 있습니다.

state에 설정된 값은 다음과 같이 수정할 수 없으며, 반드시 useState를 통해 수정이 이루어져야 합니다. 여러개의 값을 state로 설정한 기준으로 예시를 보겠습니다.

잘못된 예\(x\)

```text
    users[0].email = 'kevinGwon@gmail.com'
```

올바른 예\(O\)

```text
    let nextArr = arr.map(item => (
        item.id === 1 ? 
            {...item, email: 'kevinGwon@Gmail.com'} : 
            item
        )
    );
    setUsers(nextArr);
```

react에서 state를 다룰때 중요한점은 불변성을 유지하면서 값을 수정해야 하는 것입니다. state에 직접 접근하는것이 아닌, 배열이나 객체를 수정할때는 복사본\(spread operator\)을 만들고 그 사본에 값을 업데이트한 후 Setter함수에 전달을 해주어야 합니다.

