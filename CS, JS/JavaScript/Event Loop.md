# Event Loop

JavaScript가 싱글스레드 언어임에도 불구하고 비동기 처리가 가능한 이유에 대해 알아보자.

## Process 와 Thread

들어가기 전에, Process와 Thread 에 대해 알아보자.

- Process 란?  
  OS의 메모리에 탑재되어 실행중인 프로그램.

  Process 메모리 영역에는 Code, Stack, Heap, Data 로 구분됨.

  Stack -> 함수의 Call Stack  
  Heap -> 동적 할당  
  Data -> Static, 전역변수 등

* Thread 란?  
  Process 내부의 실행 흐름.

  한 Process내에서 Thread 들은 각자 Call Stack을 갖고 나머지 자원들은 공유하게 됨.

  Race Condition에 유의하자.

## Threading

- Multi Threading  
  여러 작업을 동시에 수행 가능.

* Single Threading  
  한번에 하나의 작업만 수행 가능.

JavaScript 는 Single Threaded 언어에 해당한다.  
JavaScript Engine은 어떻게 비동기 처리를 지원하는 것인지 알아 보자.

## Event Loop

JavaScript Engine 또한 Memory Heap, Call Stack을 가지고 있다.

- Web APIs  
  DOM API, setTimeout, fetch, eventListner 등

setTimeout, eventListner에 등록해 준 콜백을 어떻게 실행하게 될까?

setTimeout -> 타이머 작동시키고, 타이머 끝나면 등록된 콜백을 Task Queue에 추가.

JS는 Single Thread 언어이지만, 브라우저는 Multi Threading을 지원하고, Web API는 브라우저 환경에서 실행됨을 유의하자.

eventListner -> 해당 이벤트 발생시 등록된 콜백을 Task Queue에 추가.

Task Queue에 등록된 콜백은 Event Loop가 처리해 줌.

Event Loop를 추상화하면 다음과 같음.

```typescript
while (true) {
  while (callstack.isNotEmpty) {
    let run = callstack.pop();
    run();
  }

  callstack.push(taskqueue.pop());
}
```

콜스택이 빌때까지 실행.

콜 스택이 비었다면 Task Queue 의 요소 하나를 Call Stack으로 가져와 실행.

위 과정을 계속 반복(Loop)하고 있음.

간략화된 동작 과정을 살펴봤고, 다른 요소들을 더 살펴보자.

- Task Queue  
  Web API 에서 등록해주는 콜백에 해당

* Micro Task Queue  
  Promise에 등록해주는 콜백등이 해당.

  ```typescript
  let promise = Promise.resolve();

  promise.then(() => alert("프라미스 성공!"));

  alert("코드 종료"); // 얼럿 창이 가장 먼저 뜹니다.
  ```

  Promise가 바로 이행되었는데 코드 종료창이 먼저 뜨는 이유와 함께 동작을 살펴보자.

  첫번째 줄에서, Promise가 이행됨.

  두번째 줄에서, 콜백을 전달, 이행되었으므로 바로 Micro Task Queue에 push.

  세번째 줄에서, 코드종료 출력.

  Call Stack이 비었으니 Micro Task Queue에 등록된 callback Call Stack으로 가져와 실행.

  핵심 사항은, Call Stack이 빌 때 까지 실행을 보장한다는 것이다.

  Micro Task Queue에 Push 는 두번째 줄에서 이루어지지만, 세번째 줄이 끝나고서 실행되는데, Call Stack 이 비어있어야 Loop를 재개하기 때문.

Task Queue는 1 Loop 에 하나씩,  
Micro Task Queue는 1 Loop에 전부 처리됨.

- Request Animation Frame

  돔 요소의 변화가 반영되기 위해선 Render Tree, Layout, Paint, Composite 과정을 거치게 됨(이하 Render).

  RAF Queue

  RAF Queue에 콜백을 등록하면 Render 과정 전에 실행해 줌.

## Summary

Event Loop는 무한루프로, 계속 루프를 돌고 있음.

Call Stack이 비어있지 않다면 루프를 일시정지하고 Call Stack 계속 실행.

Call Stack이 비어있으면 Micro Task Queue, Task Queue를 검사하고, 만약 있다면 Call Stack 으로 가져와 실행.

가끔(주사율에 맞게, 주로 60fps) render 과정을 수행.
