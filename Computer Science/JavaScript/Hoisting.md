# Hoisting

자바스크립트는 모든 선언(let, const, 함수 ,클래스 등)을 호이스팅한다.  
호이스팅이란, 선언을 해당 스코프의 최 상단으로 옮긴 것처럼 동작하는 특성을 말한다.

그래서, 선언 이전에도 사용이 가능하다.  
var 과 let은 좀 다른데, 차이를 살펴 보자.

## var

var은 선언과 동시에 초기화가 이루어져 선언 이전에도 사용이 가능하다.  
단 할당 이전이면 undefined 를 얻겠지만.

## let

let의 경우 3단계로 나뉘며 선언단계 초기화단계, 할당 단계로 나눌 수 있다.

```typescript
// 초기화 이전 선언단계에 해당
console.log(foo); // ReferenceError: foo is not defined

let foo; // 초기화 단계 실행
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

각 단계별 렉시컬 환경을 살펴 보자.

```typescript

{
  foo : uninitialized,
  outer : null
}

```

아직 초기화되지 않은 상황이며,스코프의 최 상단에서 선언 단계가 실행된다.

```typescript
let foo;
```

```typescript

{
  foo : undefined,
  outer : null
}

```

선언되었지만 값이 할당되지 않았다. 지금부터 변수 참조가 가능하다.

```typescript
let foo = 3;
```

```typescript

{
  foo : 3,
  outer : null
}

```

값이 할당되어 3이라는 값을 참조할 수 있다.

선언 단계와 초기화 단계 사이에서 변수를 참조하면 Refrence Error 가 발생하는데, 이 구간을 TDZ, 일시적 사각지대라 한다.

얼핏 보면 호이스팅이 되지 않는게 아니냐고 할 수 있으나 다음 코드를 봐 보자.

```typescript
let x = 1;

function func() {
  console.log(x); // ?

  let x = 2;
}

func();
```

호이스팅되지 않았다면 1을 출력할 것이지만, TDZ에 걸려 Refrence Error를 발생시킨다. x의 선언이 호이스팅된 것이다.

- 함수

  함수도 호이스팅된다. 이때 함수 선언문으로 생성된 경우 var처럼 선언과 동시에 초기화가 이루어져 할당 이전에도 자유롭게 호출이 가능하다.  
  함수 표현식의 경우 변수에 함수 참조를 할당하는 방식이므로 선언 이전에 호출시 TDZ에 걸리고, 할당 이전에 호출하면 undefined을 호출한다.
