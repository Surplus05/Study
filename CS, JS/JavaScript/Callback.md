# Callback

Callback 을 단순히 다른 함수의 인자로써 이용되는 함수로 알고 있었는데 좀 더 자세히 알아보자.

## Asynchronous Function

다음과 같은 함수가 있다.

```typescript
function loadScript(src) {
  let script = document.createElement("script");
  script.src = src;
  document.head.append(script);
}
```

함수는 다음과 같이 사용한다고 하자.

```typescript
loadScript("/my/script.js");
```

이 스크립트는 로딩은 바로 시작하더라도 loadScript 호출이 완료된 후 실행되기 때문에 비동기적으로 실행된다.

실행되기 위해서는 로딩과 loadScript 호출이 완료되어야 한다.

```typescript
loadScript("/my/script.js");
// 아래 부분
```

여기서 표시한 // 아래부분 에서는 스크립트 로딩을 기다려주지 않는다.

그래서 loadScript 호출이 완료되었다 하더라도 아래 부분의 순서는 바뀔 수 있다. (비동기)

```typescript
loadScript("/my/script.js"); // script.js에 scriptFunction 이 존재한다고 하자

scriptFunction(); // 실행시 함수가 존재하지 않는다는 오류가 발생한다.
```

함수 로딩이 완료되었는지는 알 수 없다.  
그냥 스크립트를 불러와 문서에 추가하라고 요청만 할 뿐.

여기서 Callback 함수의 개념을 적용 해 보자.

```typescript
function loadScript(src, callback) {
  let script = document.createElement("script");
  script.src = src;

  script.onload = () => callback(script);

  document.head.append(script);
}
```

스크립트 로드가 끝나면 실행되는 함수 onload에 원하는 로직을 전달 해 주자.  
이게 Callback 함수이다.

```typescript

loadScript('/my/script.js', function() {
  scriptFunction(); // 이제 scriptFunction 은 정상적으로 실행될 수 있다.
  ...
});

```

이러한 방식을 콜백 기반 비동기 프로그래밍이라 한다.  
콜백을 인수로 제공해야 한다.

콜백 함수에서 새로운 스크립트를 불러오는 동작이 존재하면 어떻게 될까?

```typescript
loadScript("/my/script.js", function (script) {
  alert(`${script.src}을 로딩했습니다. 이젠, 다음 스크립트를 로딩합시다.`);

  loadScript("/my/script2.js", function (script) {
    alert(`두 번째 스크립트를 성공적으로 로딩했습니다.`);
  });
});
```

이런식으로 처리하면 된다.

## Error Handling

만약 스크립트 로드중에 연결이 끊겨 제대로 로드되지 않았다면?  
실패할 가능성을 염두해 두어야 한다.

```typescript
function loadScript(src, callback) {
  let script = document.createElement("script");
  script.src = src;

  script.onload = () => callback(script);
  script.onerror = () => callback(new Error(`Script load error for ${src}`));

  document.head.append(script);
}
```

callback 에서 error를 받아 처리할 수 있게 하자.

callback 의 매개변수를 늘리면 된다.

```typescript
function loadScript(src, callback) {
  let script = document.createElement("script");
  script.src = src;

  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`Script load error for ${src}`));

  document.head.append(script);
}
```

```typescript
loadScript("/my/script.js", function (error, script) {
  if (error) {
    // error handling
  } else {
  }
});
```

error-first callback 이라는 패턴으로 불리는데, 관례처럼 사용된다.  
첫 매개변수는 에러 처리에 사용하고, 그 이후 매개변수는 성공했을 경우 전달하는 인자를 받도록 한다.

## Callback Hell

에러 처리와 중첩 스크립트 로딩이 겹치면..?

```typescript
loadScript("1.js", function (error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript("2.js", function (error, script) {
      if (error) {
        handleError(error);
      } else {
        // ...
        loadScript("3.js", function (error, script) {
          if (error) {
            handleError(error);
          } else {
            // ...continue after all scripts are loaded (*)
          }
        });
      }
    });
  }
});
```

콜백 지옥이 펼쳐진다.  
이를 해결하기 위해서는 Promise 객체를 사용하는것이 대표적인 방법이다.
