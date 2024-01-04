# Domain Driven Design in Front-End
현 회사에서 운영중인 서비스 중 하나가 여러가지 문제점으로 인해 재설계하는 것이 비용면에서 효율적이라고 판단되었고, 내가 담당하게 되었다.  
하는김에 새로운 스택도 실무에서 사용해 보고 싶어서 마음대로 도입하게 되었다.

## 레거시는 Atomic Design Pattern 을 사용한다  
Atomic Design Pattern 은 계층 구조로, 최소 단위의 요소부터 결합하여 다음 계층 요소를 형성하는 설계방법론 중 하나이다.  
Atoms, Molecules, Organisms, Templates, Pages 라는 5 계층으로 나뉜다.  

* 마주친 문제점들  
  수정에 용의하지 않았다. 동일한 Atoms 요소를 PC와 Mobile에 둘다 사용하고 있었는데, Mobile만 다르게 보여지게 수정 요청이 들어왔었는데, 사소한 수정이었지만 간단하지가 않았다. 그렇다고 거의 동일한 Atoms 요소를 새로 만들지도 고민이 되었었다.  

  하위 요소가 상위 요소 어디에서 쓰이는지 전부 파악할 수 없으니 수정했을 경우 무슨 결과가 발생할 지 예측이 어려웠다.

  새로운 요소를 생성할 때, 고민이 많이 되었다. Atoms 와 Molecules 의 경계에 위치해 애매한 요소들이 꽤 있었다.

  프로젝트가 커짐에 따라 많은 요소들이 만들어졌고, 이름만 보고 코드 에디터에서 찾기가 엄청 힘들었다.

즉 유지보수가 어려웠다.

## Domain Driven Design  
DDD 는 Domain별로 나누어 개발하는 방법론 중 하나이다.  
Boundaries, Context Mapping, Ubiquitous Language 등을 통해 귀에 박히도록 들어왔던 Low Coupling과 High Cohesion 을 유지할 수 있다.  
새로운 기능이 추가되어야 하는 경우, 새로운 Domain을 생성하면 되므로 확장에도 용이하다.  

## DDD 적용하기

다행히 Atomic Design Pattern 을 사용중이었고 계층으로 이미 나누어져 있었기에, Domain별 구분하기가 용이했다.  
최상위 계층을 Domain화해 나누고, 구역별로 세분화했다.  

... 진행 중
