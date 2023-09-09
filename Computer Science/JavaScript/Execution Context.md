# Execution Context

실행 컨텍스트는 실행할 코드에 제공할 환경 정보들을 모아놓은 객체다.

무슨 정보가 있는지, 어떻게 동작하는지 살펴 보자.

```javascript
let a = 1;
function outer() {
  function inner() {
    console.log(a); // Reference Error
    let a = 3;
  }
  inner();
  console.log(a); // 1
}
outer();
console.log(a); // 1
```

처음 실행되면 전역 공간이므로 전역 실행 컨텍스트가 생성되어 콜스택에 푸쉬된다.

그 다음 outer가 호출되어 outer 의 실행 컨텍스트가 생성되어 콜스택에 푸쉬되고, outer 내부에서 inner가 호출되어 inner의 실행 컨텍스트가 생성되어 콜스택에 푸쉬된다.

호출이 끝나는 순서대로 콜스택에서 팝된다.

그러면 이 전역 컨텍스트에는 무슨 정보가 담기게 되냐?

Variable Environment  
Lexical Environment  
This Binding

Variable Environment는 렉시컬 환경이 만들어졌을 때의 스냅샷.

Lexical Environment 식별자의 정보를 저장하는 환경레코드와 외부 렉시컬 참조로 구성되어 있다.

실행 컨텍스트가 만들어지는 시점에는 다음과 같은 일이 발생한다.

호이스팅이 이루어짐.  
렉시컬 참조 설정.  
this 값 바인딩.

그러면 실행 컨텍스트는 누가 만들 수 있는가?

전역 공간.  
함수 호출.  
eval() 호출.

eval() 호출은 거의 사용되지 않으므로 넘어가고, 전역 공간이나 함수 호출에서 주로 사용된다
