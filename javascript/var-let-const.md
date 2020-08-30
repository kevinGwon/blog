---
title: 'Javascript - var, let, const'
date: '2019-12-15T22:13:33.000Z'
category: javascript
description: >-
  Babel을 통하여 국내에서 ES6의 문법을 적극 활용하고 있는 시대를 걷고있습니다. Babel이 없었다면 ES5에 여전히 발목 잡혀 있는
  개발자들이 많았을 것이라 생각이 듭니다. ES6이전 부터 Javascript의 개발의 길을 걷고 있다면 변수(var, let, cont)를
  혼용해서 사용할 수 있다고 생각이 듭니다. 그래서 각 변수의 특징을 알아도록 하
---

# var, let, const

## Scope

### var \(function scope\)

if문을 사용하여 block scope 처리 하였지만 block scope에 영향을 받지 않고 외부에서 접근이 가능합니다.

```javascript
(function(){
  if(true) {
    var getVariable = 'function scope';
  }
  console.log(getVariable) // function scope
});
`
```

함수 안에서 정의 되었기 때문에 function scope에 의해 접근이 불가능하여 variable is not defined 가 출력이 됩니다.

```javascript
(function(){
  function runTest() {
    var getVariable = 'function scope';
  }
  console.log(getVariable) // Uncaught ReferenceError: variable is not defined
});
`
```

### let, const \(block scope\)

if문의 block scope에 의해서 외부에서 변수에 접근할 수 없습니다.

```javascript
(function(){
  if(true) {
    let getVariable = 'function scope'; // const 동일
  }
  console.log(getVariable) // variable is not defined
});
`
```

block scope의 내부에서 접근이 가능하기때문에 다음과 같이 출력이 가능합니다.

```javascript
(function(){
  if(true) {
    let getVariable = 'function scope'; // const 동일
    console.log(getVariable) // function scope
  }
});
`
```

## Hoisting

Javscript에서 Hoisting이란 선언된 변수\(or 함수\)를 정의하면 스코프의 실행 환경에 진입하면서 미리 모든 변수\(or 함수\)를 찾아 실행환경 최상단에 정의하고 초기화 상태를 가지는 것 입니다. var와 let/const는 다음과 같이 다른 특징을 가집니다.

### var

```javascript
(function() {
  console.log(getVariable); // undefined
  var getVariable = 'kevin';
})();
`
```

변수를 선언하기 전에 출력문을 통해 미리 변수를 사용하니 오류가 아닌 undefiend을 출력하고 있습니다. 이는 Hoisting을 거치면서 해당 스코프의 변수를 찾아 최상단에 정의하여 undefined로 초기화 과정을 거치는데 이것이 바로 var의 특징입니다.

### let/const

```javascript
(function() {
  console.log(getVariable); // Cannot access 'getVariable' before initialization
  let getVariable = 'kevin';
})();
`
```

위와같이 오류가 뜨기 때문에 let,const는 호이스팅이 되지 않는다 라고 생각할 수 있습니다. 그러나 이와 같은 현상은 TDZ\(Temporal Dead Zone\)이 존재하기 때문입니다. 다음과 같이 코드를 살펴봅시다.

```javascript
const getVariable = 'kevin out';
(function() {
  console.log(getVariable); //Cannot access 'getVariable' before initialization
  const getVariable = 'kevin inner';
}());
```

다음과 같이 오류가 일어난 이유는 let,const역시 호이스팅이 되고있기 때문입니다. 이는 코드를 예측가능하고 잠재적으로 버그를 쉽게 찾아낼 수 있도록 도와줍니다.

변수는 다음과 같이 3단계에 걸쳐 생성된다. 1. 선언 단계 - 변수를 실행 컨텍스트의 변수 객체에 등록한다. 이 변수 객체는 스코프가 참조하는 대상이 된다. 2. 초기화 단계 - 변수 객체에 등록된 변수를 위한 공간을 메모리에 확보한다. 이 단계에서 변수는 undefined로 초기화된다. 3. 할당 단계 - undefined로 초기화된 변수에 실제 값을 할당한다.

var 키워드로 선언된 변수는 선언단계와 초기화 단계가 한번에 이루어진다. 변수를 위한 공간을 메모리에 확보하고 undefined로 초기화 되어 호이스팅이 된다.

let 키워드로 선언된 변수는 선언 단계와 초기화 단계까 분리되어 진행됩니다. 스코프에 변수 객체를 등록하지만 초기화 단계는 선언문에 도달 했을 때 이루어집니다. 따라서 초기화 단계가 이루어진 상태가 아니기 때문에 접근하면 참조 에러를 발생하는 것입니다. 스코프의 시작점부터 초기화 시작점까지 변수를 참조할 수 없는데 이를 TDZ\(Temporal Dead Zone, 일시적 사각지대\)라 합니다.

참조\) [poiemaweb.com](https://poiemaweb.com/es6-block-scope)

## let, const를 지향해야 하는 이유

var변수는 다음과 같은 특징을 가집니다.

* 헷갈리는 함수 레벨 스코프
* 중복 선언 가능
* 생략 가능
* 호이스팅

let, const는 다음과 같은 특징을 가집니다.

* 명확한 블럭 레벨 스코프
* 중복 선언 불가능
* strict 호이스팅

개인이 혼자 var의 개념을 이해하고 개발을 한다면 문제가 되지 않겠지만 누군가 그 코드를 이어받아 유지보수에 들어간다면 예상치 못한 곳에서 에러를 맞이할 수 있습니다. 따라서 let, const를 이용하여 코드및 버그를 예측 가능한 형태로 개발 하는것을 지향하고 추천합니다.

