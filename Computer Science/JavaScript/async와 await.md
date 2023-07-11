# async와 await

Promise를 조금 더 편하게 사용할 수 있게 해주는 문법이다.  
비동기 코드를 동기 코드처럼 보이게 작성할 수 있다.

## async function

함수 내에서 await 키워드를 사용하기 위해서는 async function 이어야 한다.

async 키워드를 함수 앞에 위치시켜 async function 을 만들자.

```typescript
async function f() {
  return 1;
}
```

함수 앞에 async키워드를 붙이면 항상 Promise 객체를 반환한다.  
위 함수는 result가 1이고 state가 fulfilled인 Promise 객체를 반환하게 된다.

그래서 f().then(alert) 처럼 사용 가능하다.

명시적으로 Promise 반환도 가능하다.

```typescript
async function f() {
  return Promise.resolve(1);
}
```

위 함수도 f().then(alert) 처럼 사용 가능하다.

## await

async 함수 내부에서만 동작하는데, 한번 사용법을 보자.

```typescript
let value = await promise;
```

await 키워드를 만나면 promise가 처리될 때 까지 기다리게 된다.

```typescript
async function f() {
  let promise = new Promise((resolve, reject) => {
    setTimeout(() => resolve("완료!"), 1000);
  });

  let result = await promise; // 기다림

  alert(result); // "완료!"
}

f();
```

이 기다리는 동안 JS 엔진이 다른 작업을 할 수 있기 때문에 리소스가 낭비되지 않는다.

체이닝 방식으로 구현된 코드를 async, await을 통해 나타내면 비동기 로직이 아닌것처럼 나타낼 수 있기 때문에 훨씬 가독성이 좋다.

## Error Handling

```typescript
async function f() {
  let promise = new Promise((resolve, reject) => {
    setTimeout(() => reject("거부됨!"), 1000);
  });

  let result = await promise; // 기다림

  alert(result); // "?"
}

f();
```

reject되면 throw new Error 가 발생한 것과 동일하게 동작한다.  
암시적 try..catch 가 존재하지 않아 전역 에러가 발생하게 된다.  
명시적 try..catch 로 해결이 가능하다.

```typescript
async function f() {
  let promise = new Promise((resolve, reject) => {
    setTimeout(() => reject("거부됨!"), 1000);
  });

  try {
    let result = await promise; // 기다림
  } catch (error) {
    alert(error); // "?"
  }
}

f();
```

또는 Promise가 rejected가 될테니, catch로도 해결이 가능하다.

```typescript
async function f() {
  let promise = new Promise((resolve, reject) => {
    setTimeout(() => reject("거부됨!"), 1000);
  });

  let result = await promise; // 기다림
}

f().catch(alert);
```

또는 전역 이벤트 핸들러 unhandledrejection 로도 해결할 수 있다.

## async/await vs then/catch

async / await을 사용하면 가독성이 좋아지기에 대부분 사용하게 됨.  
그러나 최상위 레벨에서는 await 사용이 불가능하기에 관행처럼 then/catch를 사용한다고 함.

### 요약

async function은 ..  
언제나 Promise 반환.  
함수 내부에서 await를 사용가능.

await 키워드는 ..  
처리될 때까지 대기.  
처리가 완료되면 조건에 따라 아래 동작 실행.

에러 발생 – 예외가 생성됨(에러가 발생한 장소에서 throw error를 호출한 것과 동일)
에러 미발생 – 프라미스 객체의 result 값을 반환
