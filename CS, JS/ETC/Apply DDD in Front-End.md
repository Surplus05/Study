# Apply DDD in Front-End
현 회사에서 운영중인 서비스 중 하나가 여러가지 문제점으로 인해 재설계하는 것이 비용면에서 효율적이라고 판단되었고, 내가 담당하게 되었다.  
하는김에 새로운 스택이나 방법론 등도 실무에서 사용해 보고 싶어서 마음대로 도입하게 되었다.  
방법론에 대한 자세한 내용은 이 문서에서는 다루지 않는다.  

전반적인 내용은 [FECONF 2022 [A3] 프론트엔드 DDD를 만나다](https://www.youtube.com/watch?v=FeDBlSBPUz8) 를 참고하였다.

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
또한 이미 용어 정립이 되어있었기에 유비쿼터스 언어 정의단계가 불필요했다.  

Hexagonal 구조에서, UI와 Adapter의 경우 프레임워크(라이브러리)에 해당하고 Domain, use Cases, infrastructure 부분은 프레임워크(라이브러리)에 독립적이라고 했다.  
예제를 보면 UI는 컴포넌트가 존재하며, Adapter에 접근해 상태 정보를 가져오게 된다.  
Adapter는 UI에 상태를 전달하거나, infrastructure를 통해 상태를 설정하기도 한다.    
infrastructure는 repository나 service의 구현체가 위치하는 곳이다.  
외부 시스템과의 커뮤니케이션 또한 진행된다.  
Customer의 데이터를 fetch하고 update 하는 부분은 infrastructure 내부에만 구현되어 있다.  

바깥 레이어에서는 infrastructure를 통해 데이터를 받아오고, 안쪽 레이어에서는 data의 interface에 맞춰 object를 제공해주기만 하면 됨.  
내 외부 결합도 낮출 수 있다.  

axios, fetch등 어느것을 사용해도 다른 레이어에 영향을 미치지 않는다.  
application layer에는 비즈니스 로직이 들어간 use case 들이 존재.  

domain layer는 각각의 도메인은 비즈니스에서 사용되는 entity, value object를 포함.  
타입 정보 등이 들어간다고 함.

FECONF에서 본 DDD 처럼 Hexagonal로 분리하고 다시 도메인으로 구분하는 경우 지금 상황에서는 오히려 더 복잡해 질 것 같아 Domain 별 큰 범주에서 구분하고, 로직 부분은 별도의 폴더로 분리하도록 했다.  

도메인을 직접적으로 적기는 좀 그렇고, 구조는 다음과 같이 구성했다.  

```
ROOT
└─ src
   ├─ core
   │  ├─ hook
   │  ├─ store
   │  ├─ util
   │  └─ api
   │  └─ page
   └─ domain
      ├─ ...
      ├─ ...
      └─ common
```

프로젝트의 bounded context(domain)가 사실상 page 단위로 명확히 구분되어 있으므로 page에서는 route 만 담당하면 될 것으로 예상된다.  

한마디 : 영상을 봐도 확 와닿지가 않는듯.
