---
title: React - useEffect
date: '2019-12-10T14:12:00.000Z'
category: react
description: >-
  useEffect는 리액트 라이프 사이클에 맞추어 특정작업들을 실행할 수 있습니다. useEffect함수에 두가지 파라미터를 받습니다.
  첫번째 파라미터는 함수, 두번째 파라미터는 deps(의존값 배열)를 받으며 다음과 같이 3가지 특징을 가집니다.
---

# useEffect

**1. deps가 없을경우 컴포넌트가 렌더링 될 때마다 호출됩니다.**

```text
useEffect(()=>{
   console.log('useEffect on!!');
});
```

**2. deps가 빈 배열일 경우 컴포넌트가 생성될 때, 제거될 때 한 번만 실행됩니다.**

```text
useEffect(()=>{
   console.log('useEffect on!!');

    return () => { 
        console.log('cleanup!!';
    }
}, []);
```

**3. deps에 의존값이 있을경우 다음과 같이 실행 됩니다.**

* 컴포넌트 생성될때 실행  
* 상태값이 변경되면 cleanup함수 -&gt; 첫번째 파라미터의 함수 순서로 실행  
* 컴포넌트 제거시 cleanup함수만 실행

  ```text
  useEffect(()=>{
  console.log('useEffect on!!');

  return () => { 
    console.log('cleanup!!';
  }
  }, [deps]);
  ```

위와 같이 정의하게되면 리액트의 라이플 사이클을 통해 Mount, unMount, Update의 시점에서 코드들을 컨트롤 할 수 있습니다.

