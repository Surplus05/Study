# Closure 와 Lexical Enviroment

## Block Scope

블록 내부에서 선언 한 변수는 블록 내부에서만 사용이 가능하다.

```typescript
{
  let count: number = 0;
  console.log(count);
}

console.log(count); // Cannot find name 'count'.
```

블록 내부에서는 중복 선언이 불가능하다.

```typescript
let count: number = 0;
let count: number = 0; // Cannot redeclare block-scoped variable 'count'.
```

if, for, while, switch 등 블록을 사용하는 경우 동일하게 적용된다.  
단, for의 경우 블록 외부지만 블록 내부 취급.

```typescript
for (let i = 0; ; i++) {
  console.log(i);
}

console.log(i); // Cannot find name 'count'.
```

이를 함수에 적용시켜 보자.

```typescript
function makeCounter() {
  let count = 0;

  return function () {
    return count++;
  };
}

let counter = makeCounter();
let counter2 = makeCounter();

console.log(counter()); // 0
console.log(counter()); // 1
console.log(counter()); // 2

console.log(counter2()); // ?
```

counter2 의 출력값은 0이 출력된다.  
그 이유를 살펴 보자.

## Lexical Enviroment

함수나 블록등 스코프는 각자 렉시컬 환경이라는 객체를 갖고 있다.  
렉시컬 환경 객체는 두 부분으로, 환경 레코드와 외부 렉시컬 참조로 구성된다.

- Variable

  변수는 이 렉시컬 환경 객체의 프로퍼티다.  
  즉 함수나 블록에 변수가 선언되면, 이 렉시컬 환경에 등록되어 관리된다는 것이다.

  ```typescript
  {
    let count: number = 0;
    console.log(count);
  }
  ```

  위 블록의 렉시컬 환경 객체는 다음과 같이 이루어 진다.

  ```typescript
  {
    count: 0;
    outer: Outer Lexical Refrence;
  }
  ```

  제일 최 상위 렉시컬 환경을 Global Lexical Enviroment 라고 하는데, 이 렉시컬 환경은 외부 렉시컬 참조가 null이다.  
  단, 이 객체는 직접 얻거나 명시적 조작이 불가능하다.

* Function  
   함수는 렉시컬 환경에서 어떻게 관리될까?

  ```typescript
  a();
  function a() {}
  ```

  ```typescript
  {
  a: function;
  outer: null;
  }
  ```

  함수 또한 변수와 동일하게 관리되는데, 함수 생성방식이 선언문이냐 표현식이냐에 따라 TDZ가 발생할 수 있다. -> Hoisting에서 따로 다루자.  
  이제 변수를 참조하는 매커니즘을 알 수 있다.  
  참조가 발생하면 우선 자신 블록의 렉시컬 환경을 찾아보고, 없다면 outer의 렉시컬 환경을 찾아본다.  
  전역 레시컬 환경에도 없다면 정말 없는 것이다.

다시 위에서의 함수를 한번 살펴 보자.

```typescript
function makeCounter() {
  let count = 0;

  return function () {
    return count++;
  };
}
```

함수를 리턴할 때, 함수 블록의 렉시컬 환경까지 같이 리턴되는데 이때 외부 렉시컬 참조는 선언될 당시의 외부 스코프 렉시컬 환경이다.  
이러한 개념을 Closure

즉, 선언될 당시의 환경을 기억하고 있는 것이다.

makeCounter를 호출해 counter1을 만들면 새로운 렉시컬 환경이 만들어지고, count가 0으로 초기화 된다.  
이 렉시컬 환경이 같이 반환되어 호출시 count를 증가시킨다.

makeCounter를 다시 호출해 counter2를 만들면 또 새로운 렉시컬 환경이 만들어지고 다시 0부터 카운트 된다.

## Garbage Collection

일반적으로 호출이 끝나면 함수 블록의 렉시컬 환경이 메모리에서 제거되어 사라지는데 만약 도달 가능한 함수가 존재한다면 사라지지 않는다.

```typescript
function f() {
  let value = 123;

  return function () {
    alert(value);
  };
}

let g = f();
```

g에서 f의 렉시컬 환경에 도달가능하기 때문에 f의 렉시컬 환경은 살아있게 되고, value에 접근이 가능하다.

```typescript
g = null;
```

위와 같이 도달이 불가능하게 만들면 메모리에서 사라지게 된다.

크롬이나 오페라같은 V8 엔진에서는 최적화 과정에서 위 렉시컬 환경을 사용하지 않는다고 판단해 지워버리는 경우가 있다.

```typescript
let value = "outer";

function f() {
  let value = "inner";

  function g() {
    debugger; // 콘솔에 alert(value); 시 outer 출력
  }

  return g;
}

let g = f();
g();
```

배운대로라면 inner를 출력해야 하지만 outer를 출력하는데, V8만의 특별한 기능.
