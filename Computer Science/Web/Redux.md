# Redux

Redux 는 전역 상태관리 라이브러리.  
Redux를 사용하면 상태관리 로직을 컴포넌트와 분리시켜 효율적으로 관리할 수 있다.  
또한, Prop Drilling 을 피할 수 있다는 장점이 있다.

Redux의 핵심 개념은 Single Source of Truth인데, 이는 Redux store라는 중앙 저장소를 두기 때문. State의 변경은 불변성을 유지하면서 Redux에서 정의된 액션(action)을 통해 이루어 진다.

- Flux 패턴  
  Flux 패턴은 사용자 입력을 기반으로 Action을 생성하고, 이를 Dispatcher에 전달하여 Store의 데이터를 변경한 뒤 View에 반영하는 단방향의 데이터 흐름을 가지는 아키텍처.  
  Flux 패턴으로 데이터가 단방향으로만 전달되기 때문에 흐름 파악이 용이하고, 그 결과를 쉽게 예측할 수 있다는 장점을 갖는다.

* Redux 동작 원리  
  Redux는 Store에 state와 Reducer를 저장한다.  
  Redux는 하나의 Store만을 생성하는데, Store를 변경하기 위한 로직을 저장하는 곳이 바로 Reducer이다.  
  현재 state와 Action을 받아 Store에 접근하고, 전달 받은 Action을 참고하여 새로운 state를 만들어 반환한다.  
  어느 로직이 필요한지 결정하는것은 Action 인데, state에 변화가 필요할 때 Action을 발생시켜(Dispatch) Reducer에 전달하게 된다.

  정리하자면 상태를 변경하기 위해 발생시키는 이벤트를 액션(Action)이라고 부른다. 액션은 객체이며, 'type'을 통해 어떤 종류의 액션이 발생했는지 식별이 가능하다.  
  "UPDATE_STATE", "FETCH_DATA" 등 문자열을 통해 식별한다.  
  리듀서(Reducer)는 액션에 따라 상태를 변경하는 로직이다.  
   이전 상태와 액션을 인자로 받아 새로운 상태를 반환하며, 리듀서는 불변성을 유지하기 위해 항상 새로운 객체를 반환한다.
