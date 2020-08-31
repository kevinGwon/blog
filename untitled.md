---
title: React - props
date: '2019-11-29T14:12:20.000Z'
category: react
---

# Props

컴포넌트에 props를 넘겨줄때 속성명과 속성값을 다음과 같이 넘기게 됩니다.

_\(같은 디렉토리에 Hello 컴포넌트가 있다는 가정하에 작성을 해보도록 하겠습니다.\)_

App.js

```text
import Hello from './hello';

<Hello name="Kevin" city="Seoul" />
```

위와같이 Hello 컴포넌트에 속성명과 속성값을 넘겨 주었으며 Hello 컴포넌트에서는 다음과 같이 파라미터로 데이터를 받을 수 있습니다.

Hello.js

```text
function Hello(props) {
    return <div>
        My name is {props.name}!!! <br />
        I live in {props.city}
    </div>;
}
```

또한, props를 다음과 같이 객체로 받아올 수 있고 파라미터의 순서\(객체이기 때문에\)를 고려하지 않아도 됩니다. 그리고 props의 변수명을 생략할 수 있습니다.

```text
function Hello({city, name}) {
    return <div>
        My name is {name}!!! <br />
        I live in {city}
    </div>;
}
```

## defaultProps

defaultProps는 props의 기본값을 셋팅 해주는 속성입니다. 위의 내용에 이어서 컴포넌트에 속성을 기본값으로 보여주고 싶다면 다음과 같이 설정할 수 있습니다.

App.js

```text
import Hello from './hello';

<Hello city="Seoul" />
```

Hello.js

```text
function Hello({city, name}) {
    return <div>
        My name is {name}!!! <br />
        I live in {city}
    </div>;
}

Hello.defaultProps = {
    name: '홍길동'
};
```

Hello 컴포넌트에 값을 전달해주는 name을 제거하여 값을 전달 받을 수 없습니다. 하지만 하단에 defaultProps를 이용하여 기본값을 지정해주어 '홍길동'이란 이름으로 대체가 되었습니다. 이러한 기능을 이용하면 UI/UX에 도움이 될 것입니다.

## children

컴포넌트를 단순히 감싸는 용도로 `<div>`태그를 이용할 수 있지만, 컴포넌트를 이용하여 감싸게 될 경우 어떻게 될까요? 다음과 같은 코드의 형태가 나오게 됩니다.

App.js

```text
import Wrapper from "./Wrapper";

<Wrapper>
    <Hello city="Daejeon"/>
</Wrapper>
```

Wrapper.js

```text
import React from 'react';

function Wrapper({ children }) {
    return <div className="wrapper-box">{children}</div>;
}

export default Wrapper;
```

반드시 컴포넌트의 형태로 내용들을 감앃을 경우 파라미터로 children을 받아와 태그안에 children을 선언해 주어 렌더링을 해주어야 화면에 표시가 되니 반드시 기억하시기 바랍니다.

