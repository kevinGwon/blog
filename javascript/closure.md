---
title: javascript - 클로저
date: '2019-12-04T00:12:08.000Z'
category: javascript
---

# 클로저

클로저란 내부 함수에서 외부 함수에 선언된 변수를 참조하는 하나의 스코프 형태를 의미합니다. 클로저를 대표적으로 사용하는 이유는 변수를 외부에서 접근할 수 없게 privite 형태로 사용할 수 있기 때문입니다. 기본적인 클로저 형태의 코드를 살펴봅시다. 

```text
function outer() {
    let count = 0,
        inner = () => count++;

    return inner;
}

let increase = outer();

increase(); // 1
increase(); // 2
```

count의 변수는 outer의 로컬 변수이고, 내부에 함수를 선언하여 count를 1증가 시키는 향테로 inner변수에 할당하였습니다. 그리고 increase변수에 outer함수를 할당하여 increase 함수를 실행할 때마다 1증가된 값을 받아올 수 있습니다. count변수는 outer함수의 로컬 변수로써 외부에서 접근 할 수 없지만 increase 함수를 통하여 내부의 값을 받아올 수 있습니다. 이것이 privite형태이고 클로저의 기본 개념입니다 .

## 스코프 체인

```text
function sum(base) {
    let inClosure = base;
    return (adder) => inClosure + adder;
}

let fiveAdder = sum(5);
fiveAdder(3);
let threeAdder = sum(3);
```

sum 함수를 호출하면 파라미터 base를 통해서 넘어온 값은 inClosure 변수에 저장된다. 그리고 내부 함수에서는 inClosure 변수를 참조한다. 이와 같은 코드가 실행될 때 스코프 체인이 생성되는 구조를 살펴보면 다음과 같다.

```text
fiveAdder, threeAdder // global scope (A함수)
↑  
sum(base) { inClosure, base } // local scope  
↑  
function(adder) { adder } // local scope (A함수)
```

fiveAdder를 통해 함수를 호출하게 되면 위의 스코프 체인을 따르게 되며 global scope에서 끝난다. 여기서 중요한 keypoint는 global scope에서 fiveAdder가 가지고 있는 것은, 하단 A함수까지 스코프 체인을 가지는 것이 아닌 함수A에 대한 레퍼런스이다. 이 스코프 체인은 함수A 를 가리키는 레퍼런스가 사라질 때까지 계속 남아있게된다.

## 메모리

클로저를 사용할 경우 메모리에 할당 됨으로써 하나의 커다란 클로저를 생성하기보단, 각 함수및 변수의 생명주기를 분석하여 효율적으로 사용하는것을 목적에 두어야한다. 특히 IE최신 버전에서는 해결되었지만, 구버전에서는 이벤트가 할당된 DOM을 삭제하면 메모리 누수까지 발생 함으로써 생명주기를 잘 파악하여 사용하는것을 권장한다.

