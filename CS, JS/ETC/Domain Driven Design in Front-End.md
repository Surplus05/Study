# Domain Driven Design in Front-End  
[FECONF 2022 [A3] 프론트엔드 DDD를 만나다](https://www.youtube.com/watch?v=FeDBlSBPUz8) 를 보고 정리한 내용입니다.  
솔직히 절반은 무슨 말인지 모르겠지만..  

## DDD Concepts  
DDD는 Strategic Design(전략적 설계)와 Tactical Design(전술적 설계)로 나뉜다.  

* Strategic Design
  Bounded Context는 하나의 이름이 가지는 의미가 통용되는 범위를 결정. 이 범위를 통해 중복된 개념과 Conflict 방지. 고객이 바라보는 상품과 주문이 바라보는 상품이 다를 수 있다. 하나의 모델로 관리되기 힘들다. 사용되지 않는 상태를 잘못 사용하는 문제, 사소한 변경이 문제를 야기할 수 있다.
  각각의 도메인에 맞게 모델을 만들어야 한다. 하나의 모델이 의미를 갖는 범위가 Bounded Context. Sales와 Support에서의 Customer가 다르기에, Context별 각각 정의를 하고 개념을 구분짓자.  
  (Context와 Domain이 동일한 의미로 사용되는 것 같다.)
  
  Ubiquitous Language는 공통적인 언어를 만들고 이를 통해 커뮤니케이션에서 발생되는 문제 방지나 비용 절감에 초점.
  상품을 추가하는 기능, 제품을 등록하는 기능을 예시로 들어보자.
  같은 기능일 수 있고, 다른 기능일 수 있기에 문제가 발생할 수 있다.
  팀 내에서 모두가 이해하는 공통된 언어라 볼 수 있다.

* Tactical Design  
  도메인 모델을 구성하기 위한 기술적인 리소스들의 집합.  
  리소스들은 하나의 Bounded Context 안에서 적용되어 돌아가게된다.
  올바르게 사용되면 Domain Model을 풍부하게 확장할 수 있고, 비즈니스 요구를 잘 반영할 수 있다.

  5가지 개념 Entity, Value Object, Aggregate, Services, Domain Events 에 대해 알아보자.

  ### Entity
  고유한 식별자가 있는 변경 가능한 객체.
  도메인 내에서 고유한 수명을 갖는다.

  ### Value Object
  Entity 와 유사하지만, 고유한 id가 없고 속성값으로만 정의된다.
  Immutable하다.

  ### Aggregate
  Entity, Value Object를 기반으로 하는 Tactical Design 설계의 가장 중요하고 복잡한 패턴 중 하나.
  하나 이상의 Entity 의 클러스터. Value Object를 포함할 수 있다.
  클러스터의 최상위 Entity 는 Aggregate Root라는 이름을 갖는다.  
  (여기부터 이해가 잘 안됨)  
  데이터의 변경을 위한 단일 유닛이 된다.    
  모든 작업이 일관적으로 종료되게 하는 경계가 된다.

  ### Service
  상태를 유지하지 않고 일부 로직을 구현하는 객체, Application Service와 Domain Service등이 있다.  
  (이 부분도 잘 이해되지 않음)

  ## Domain Events
  Entity 나 Aggregate 가 변경될떄 Domain Events가 트리거되고, 누군가가 Listen해서 어떠한 Action이 발생한다.  

## Layered  
UI계층은 인터페이스와 Application 이 연결되는 곳.   
Application계층 은 Domain 객체의 직접적인 Client, (비즈니스 상에서 필요로 할 때는) Use Case를 구현한 객체들의 집합이라고 부를 수 있다.  
Domain계층은 도메인과 관련된 객체들의 집합. Entity, Value Object, Domain Service 등이 위치.  
Infrastructure계층은 다른 계층을 지탱하는 기술적 기반. 다른 계층들의 실제 구현체.  
추상화된 도메인을 구체적으로 구현한 구현체가 Infrastructure 라 할 수 있겠다.  

## Hexagonal  
Layered에서 Interface(Adapter, Port) 가 추가됨.  
부분적인 수정이 필요할 경우 Adapter 부분만 변경해 변경을 핸들링할 수 있게 한다.  
Domain, UseCases, Infrastructure는 프레임워크에 독립적이고, UI나 Adapter는 흔히 React, Vue 등으로 부를 수 있겟다.  

* Hexagonal System Flow
![Hexagonal](https://github.com/Surplus05/Study/assets/104773096/a6d69c46-9fac-4a5e-9c91-365b2c633a57)

* Hexagonal을 적용한 폴더 구조
  ![Hexagonal Folder](https://github.com/Surplus05/Study/assets/104773096/87cfc265-ddb7-4a8c-a4eb-e9e3ec7d0456)
  실제 적용은 영상을 보며 설명 들으면서 해 보자.


