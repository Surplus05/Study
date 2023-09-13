  # React Portal

  다음 코드를 한번 보자.  

``` tsx
import React from "react";
import "./App.css";

const Modal = () => {
  return (
    <>
      <section className="modal">React Modal Child</section>
      <div className="modal-outside"></div>
    </>
  );
};

function App() {
  return (
    <div className="App">
      <div className="header">항상 맨 앞에 위치하는 컴포넌트</div>
      <h1>React Modal Parent</h1>
      <Modal />
    </div>
  );
}

export default App;
```

헤더의 z-index를 높게 주어서 항상 맨 앞에 위치한다.  
이 경우, 모달이 팝업 된 상태라도 헤더가 앞에 나와있는 현상을 경험할 수 있다.  

여기서는 Modal 과 헤더가 형제 관계이기 때문에 상관이 없겠지만,  
만약 헤더와 형제인 모달 조상보다 헤더의 z-index가 더 높다면, 모달의 z-index를 아무리 높게 주더라도 부모의 z-index가 헤더보다 낮으므로 모달보다 앞으로 나오는 현상을 볼 수 있다.  

![image](https://github.com/Surplus05/Study/assets/104773096/1601a2c5-ce50-49be-95a6-a19aeab0cdda)  

위와 같은 상황에서 React-Portal 이 유용하게 쓰일 수 있다.  

# Portal
> Portal은 부모 컴포넌트의 DOM 계층 구조 바깥에 있는 DOM 노드로 자식을 렌더링하는 최고의 방법을 제공합니다.

``` ts
ReactDOM.createPortal(child, container)
```

child 는 렌더링할 컴포넌트, container 는 컴포넌트의 부모.  

그러면 위 코드를 바꿔 보자.  

``` tsx
import React, { useEffect, useState } from "react";
import "./App.css";
import { createPortal } from "react-dom";

const Modal = () => {
  return (
    <>
      <section className="modal">React Modal Child</section>
      <div className="modal-outside"></div>
    </>
  );
};

function App() {
  const [modalContainer, setModalContainer] = useState<Element>();

  useEffect(() => {
    const el = document.getElementsByClassName("Modal")[0];
    if (el) setModalContainer(el);
  });

  return (
    <>
      <div className="App">
        <div className="header">항상 맨 앞에 위치하는 컴포넌트</div>
        <h1>React Modal Parent</h1>
        {modalContainer && createPortal(<Modal />, modalContainer)}
      </div>
      <div className="Modal" />
    </>
  );
}

export default App;
```

```tsx
{modalContainer && createPortal(<Modal />, modalContainer)}
```
이 코드가 위치한 곳은 App 밑이지만, 실제 위치는 Modal 밑에 위치한다.

![image](https://github.com/Surplus05/Study/assets/104773096/0128e9d9-42b0-4b7d-a1d3-9dd365c84c4f)  

맨앞에 위치하는 컴포넌트 또한 모달의 뒤로 보낼 수 있게 된다.  

![image](https://github.com/Surplus05/Study/assets/104773096/6a665fe4-80de-433d-b36f-20290e895a70)  

> portal이 DOM 트리의 어디에도 존재할 수 있다 하더라도 모든 다른 면에서 일반적인 React 자식처럼 동작합니다.  

심지어 이벤트 버블링도 포함된다.  
그래서 모달의 이벤트를 delegate 해 처리하려면 App 에서 처리해야 한다.
