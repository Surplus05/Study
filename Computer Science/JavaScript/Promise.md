# Promise

Promise는 프로미스가 생성된 시점에는 알려지지 않았을 수도 있는 값(비동기 연산)을 위한 객체로, 비동기 연산이 종료된 이후에 결과 값과 실패 사유를 처리하기 위한 처리기(콜백 함수)를 연결할 수 있다.  
프로미스를 사용하면 비동기 메서드에서 마치 동기 메서드처럼 값을 반환할 수 있는데, 비동기 연산의 최종 결과를 반환하는 것이 아니고, 미래의 어떤 시점에 결과를 제공하겠다는 '약속'(프로미스 객체)을 반환.

promise 객체는 아래와 같이 만들수 있다.

```typescript
let promise = new Promise(function (resolve, reject) {
  // executor
});
```

이 때 executor는 Promise 객체가 만들어 질 때 자동으로 실행되며, 로직이 수행된다.  
로직 수행이 끝나면 executor는 성공 여부에 따라 resolve나 reject를 호출하게 된다.

이 때, 넘겨준 콜백을 반드시 실행해야 한다.

```typescript
resolve(value); // 일이 성공적으로 끝난 경우 그 결과를 나타내는 value와 함께 호출
reject(error); // 에러 발생 시 에러 객체를 나타내는 error와 함께 호출
```

Promise 객체의 내부를 들여다 보자.  
Promise 객체는 state와 result라는 두개의 프로퍼티를 갖고 있다.

state는 pending으로 시작해서 resolve가 호출되었다면 fulfilled 로 변하고 reject가 호출되었다면 rejected 로 변한다.  
result는 undefined로 시작해서 resolve가 호출되었다면 value로 변하고 reject가 호출되었다면 error로 변한다.

한번 해 보자.

```typescript
let promise = new Promise(function (resolve, reject) {
  // promise 객체 생성시 executor 자동 실행

  setTimeout(() => resolve("완료"), 1000);
  // 1초 후 promise의 state는 fulfilled로, result는 "완료"로 변경되며 resolve("완료")가 호출된다.
});
```

```typescript
let promise = new Promise(function (resolve, reject) {
  // promise 객체 생성시 executor 자동 실행

  setTimeout(() => reject(new Error("에러 발생!")), 1000);
  // 1초 후 promise의 state는 rejected로, result는 Error 객체로 변경되며 reject(에러 객체)가 호출된다.
});
```

한번 상태가 변하면 다시 변하지 않는다.

```typescript
let promise = new Promise(function (resolve, reject) {
  resolve("완료");
  reject(new Error("…")); // 무시됨
  setTimeout(() => resolve("…")); // 무시됨
});
```

state와 result는 직접 접근이 불가능하다.  
then, catch, finally 를 통해야 함.

promise객체는 executor 와 콜백함수를 이어주는 역할을 한다.

## then, catch

```typescript
promise.then(
  // resolve 로 전달되는 callback 함수
  function (result) {},

  // reject 로 전달되는 callback 함수
  function (error) {}
);
```

promise executor 에 따라 어떻게 실행되는지 보자.

```typescript
let promise = new Promise(function (resolve, reject) {
  setTimeout(() => resolve("완료!"), 1000);
});

promise.then(
  (result) => alert(result), // 1초 후 "완료!"를 출력
  (error) => alert(error) // 실행되지 않음
);
```

```typescript
let promise = new Promise(function (resolve, reject) {
  setTimeout(() => reject(new Error("에러 발생!")), 1000);
});

promise.then(
  (result) => alert(result), // 실행되지 않음
  (error) => alert(error) // 1초 후 "Error: 에러 발생!"을 출력
);
```

성공한 경우면 처리하고 싶다면, 하나의 callback 만 넘겨주면 resolve로 전달된다.  
실패한 경우만 처리하고 싶다면, 하나의 callback 을 catch 로 넘겨주면 reject로 전달된다.

```typescript
.catch(f);
.then(null,f);
```

두개는 동일하다.

## finally

try-catch 에 finally가 있는 것 처럼, 동일하게 finally가 존재한다.

성공, 실패 여부 상관없이 작업을 처리하고 싶을 때 사용한다.

```typescript
new Promise((resolve, reject) => {
  setTimeout(() => resolve("결과"), 2000);
})
  .finally(() => alert("준비됨."))
  .then((result) => alert(result));
```

finally 가 먼저 실행되는데, finally 의 위치에 따라 실행순서가 달라질 수 있다.

```typescript
new Promise((resolve, reject) => {
  setTimeout(() => resolve("결과"), 2000);
})
  .then((result) => alert(result));
  .finally(() => alert("완료."))
```

.then(() => {}).finally(() => {}) 처럼 연결된 형태로 사용할 수 있다.

.then(() => {}).then(() => {}) 이런식도 가능한데, 이는 프로미스 체이닝에서 다루자.

callback은 하나만 전달이 가능하지만, then 을 여러번 호출하면 여러개의 callback 전달이 가능해 진다.

- 대기하는 함수

  여기까지 배웠으면 대기하는 함수를 직접 작성해 볼 수 있다.

  ```typescript
  function delay(ms) {
    return new Promise((resolve, reject) => setTimeout(resolve, ms));
  }
  ```

  reject되는 경우는 없으니 제외시켜 주면

  ```typescript
  function delay(ms) {
    return new Promise((resolve) => setTimeout(resolve, ms));
  }
  ```

  Promise 객체를 반환하는 delay 함수를 만들었다.

## Promise Chaining

비동기 처리를 체인 형태로 다룬다고 했다.  
 아래의 코드를 한번 보자.

```typescript
new Promise(function (resolve, reject) {
  setTimeout(() => resolve(1), 1000); // (*)
})
  .then(function (result) {
    alert(result); // 1
    return result * 2;
  })
  .then(function (result) {
    alert(result); // 2
    return result * 2;
  })
  .then(function (result) {
    alert(result); // 4
    return result * 2;
  });
```

1, 2, 4의 순서로 결과가 나타난다.  
 어떻게 이런 방식으로 동작이 가능할까?

then 호출의 결과로 Promise객체를 반환하기 때문이다.  
 다음 코드를 보자.

```typescript
new Promise(function (resolve, reject) {
  setTimeout(() => resolve(1), 1000);
})
  .then(function (result) {
    alert(result); // 1

    return new Promise((resolve, reject) => {
      setTimeout(() => resolve(result * 2), 1000);
    });
  })
  .then(function (result) {
    alert(result); // 2

    return new Promise((resolve, reject) => {
      setTimeout(() => resolve(result * 2), 1000);
    });
  })
  .then(function (result) {
    alert(result); // 4
  });
```

두개의 코드의 다른점은 위 코드는 1초후 1, 2, 4가 전부 바로 반환되는데
아래 코드는 1, 2, 4 사이에 1초간 딜레이가 생기게 된다.

정리하자면 다음과 같다.  
.then 또는 .catch, .finally의 호출 결과로 Promise객체를 반환하며, 나머지 체인은 Promise가 처리될 때까지 대기한다.  
처리가 완료되면 Promise객체의 result가 다음 체인으로 전달된다.

```typescript
// 1번
promise.then(f1).catch(f2);

// 2번
promise.then(f1, f2);
```

두개는 같을까? 아니다.

1번의 f2는 f1이 executor 가 되는 promise 를 리턴받아 에러 처리가 가능하지만 2번의 f2는 promise의 executor만 에러 처리가 가능하고 f1에서 발생한 에러 처리가 불가능하다.

실제로 한번 해 보자.

```typescript
function f1(result) {
  console.log(result + " in f1");
  return new Promise((resolve, reject) => {
    setTimeout(() => reject("에러 발생함."), 1000);
  });
}

function f2(error) {
  console.log(error + " in f2");
}
```

```typescript
let promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve("f1 실행됨."), 1000);
}).then(f1, f2);
```

2번처럼 실행하면 처음에 "f1 실행됨. in f1" 라는 메시지가 콘솔창에 출력되고, 다음에 "Uncaught (in promise) 에러 발생함." 라는 에러가 출력된다.

1번처럼 실행 해 보자.

```typescript
let promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve("f1 실행됨."), 1000);
})
  .then(f1)
  .catch(f2);
```

f1 실행됨. in f1  
에러 발생함. in f2

라는 메시지가 차례로 콘솔에 출력된다.  
f1에서 반환한 Promise 객체의 reject를 f2가 catch 해 처리해 준 것이다.

```typescript
let promise1 = new Promise((resolve, reject) => {
  setTimeout(() => reject("실행 거절됨."), 1000);
}).then(f1, f2);

let promise2 = new Promise((resolve, reject) => {
  setTimeout(() => reject("실행 거절됨."), 1000);
})
  .then(f1)
  .catch(f2);
```

만약 promise의 executor에서 처리가 실패하는 경우 어떻게 될까?  
동일하게 "실행 거절됨. in f2" 메시지가 출력된다.

어느 방식이 더 직관적인가?  
1번이다.

실제로도 예제 코드나 깃허브등을 보면 1번 패턴이 대부분 이용된다.  
가장 가까운 reject 핸들러로 스킵되는 것이다.

```typescript
.then(f1).then(f2).then(f3).catch(f4).then(f5).catch(f6)
```

f1, f2, f3 에서 발생한 reject는 f4에서, f5에서 발생한 reject는 f6에서 처리된다.  
정상 작동하는 경우 f1, f2, f3, f5 순서로 실행되어 catch는 실행되지 않는다.

## 암시적 try catch

promise의 executor는 보이지 않는 try-catch 문이 존재한다.

```typescript
new Promise((resolve, reject) => {
  throw new Error("에러 발생!");
}).catch(alert); // Error: 에러 발생!

new Promise((resolve, reject) => {
  reject(new Error("에러 발생!"));
}).catch(alert); // Error: 에러 발생!
```

그래서 위의 두 코드의 실행결과는 같다.  
명시적으로 던진 에러 뿐 아니라, 모든 종류의 에러가 이 암시작 try-catch 를 통해 처리된다.

```typescript
new Promise((resolve, reject) => {
  resolve("OK");
})
  .then((result) => {
    blabla(); // 존재하지 않는 함수
  })
  .catch(alert); // ReferenceError: blabla is not defined
```

```typescript
new Promise((resolve, reject) => {
  resolve("OK");
})
  .then((result) => {
    const a = result;
    a = 3;
  })
  .catch(alert); // TypeError: Assignment to constant variable.
```

그래서 마지막의 catch는 try..catch 와 동일한 역할을 한다.  
마지막에 catch 하나를 통해 상위 then에서 발생한 모든 에러를 잡아낼 수 있다.

다음 코드를 한번 보자.

```typescript
// 실행 순서: catch -> catch
new Promise((resolve, reject) => {
  throw new Error("에러 발생!");
})
  .catch(function (error) {
    if (error instanceof URIError) {
      // 에러 처리
    } else {
      alert("처리할 수 없는 에러");
      throw error; // 에러 다시 던지기
    }
  })
  .then(function () {
    /* 무시됨. */
  })
  .catch((error) => {
    alert(`알 수 없는 에러가 발생함: ${error}`);
  })
  .then(() => {
    alert("실행 종료");
  });
```

처리할 수 없는 에러 -> 알 수 없는 에러가 발생함: 에러 발생! -> 실행 종료  
순으로 실행된다.

에러 처리를 안해주면 어떻게 될까?

```typescript
new Promise(function () {
  throw new Error("unhandled");
}).then(() => {
  console.log("성공!");
});
```

스크립트가 죽고 전역에러가 발생해 콘솔창에 출력된다.

참고로 브라우저 환경에서는 unhandledrejection 를 통해 해결할 수 있다.

전역에러를 catch 해준다.

```typescript
window.addEventListener("unhandledrejection", (event) => {
  // event에는 두 개의 특수 프로퍼티 존재.
  alert(event.promise); // 에러가 발생한 Promise 객체
  alert(event.reason); // 처리하지 못한 에러 객체
});
```

```typescript
new Promise(function (resolve, reject) {
  setTimeout(() => {
    throw new Error("에러 발생!");
  }, 1000);
}).catch(alert);
```

그러면 위 코드는 어떻게 될까?  
에러를 잘 처리할 수 있을까?

대답은 No.

왜? setTimeout호출 후 1초 지나야 에러가 발생하는데, setTimeout 호출이 끝나면 executor 실행이 끝나 에러를 잡을 수 없다.

```typescript
new Promise(function (resolve, reject) {
  setTimeout(() => {
    reject("에러 발생!");
  }, 1000);
}).catch(alert);
```

```typescript
new Promise(function (resolve, reject) {
  setTimeout(() => {
    resolve("안녕");
  }, 1000);
}).then((result) => {
  alert(result);
});
```

이런 경우는 어떻게 될까?  
의도한 대로 정상 실행된다.

다음에는 async와 await을 다루어 보자.
