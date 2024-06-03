# socket 객체 관리하기

Socket.io를 이용하여 서버와의 실시간 통신을 구현할 때, 특히 리액트 애플리케이션에서 페이지 이동 시에도 소켓 연결을 유지하고 싶다면 소켓 관리를 어디서, 어떻게 할지 고민해봐야 한다.  

## 기본적인 소켓 연결 코드
먼저 기본적인 소켓 연결 코드를 작성해 보자.  

```jsx
useEffect(() => {
  const socket = io(SERVER_URL);

  socket.on("message", (message) => {
    // ...
  });

  return () => {
    socket.disconnect();
  };
}, []);
```

이 코드에서는 컴포넌트가 마운트될 때 소켓을 연결하고 언마운트될 때 소켓 연결을 해제한다.  

기능을 확장해 보자.

다른 주소로 이동하더라도 소켓 연결을 유지시키고 싶다.  
가장 쉽게 생각할 수 있는 방법은 공통된 부모 요소에 해당 코드를 삽입하는 것이다.   
부모 요소를 벗어나지 않는 한 자식 컴포넌트에서는 소켓 연결이 유지된다.

하위 컴포넌트에서 이벤트가 발생했을때, socket 객체에 직접 접근해 emit을 호출하고 싶은 경우 socket 객체에 어떻게 접근하는것이 좋을까?

Custom hook 을 통해 관리해 보자.  

```jsx
const useSocket = (SERVER_URL) => {
  const [socket, setSocket] = useState(null);

  const connect = () => {
    const newSocket = io(SERVER_URL);
    setSocket(newSocket);
  };

  const disconnect = () => {
    if (socket) {
      socket.disconnect();
      setSocket(null);
    }
  };

  return { connect, disconnect, socket };
};
```

```js
const { connect, disconnect, socket } = useSocket(SERVER_URL);
```
자식 컴포넌트에 해당 코드가 위치하는 경우 socket은 독립적이므로 당연히 제대로 동작하지 않는다.  

그렇다면 부모 컴포넌트에 해당 코드를 위치시켜 보자.

```jsx
const ParentComponent = ({ SERVER_URL }) => {
  const { connect, disconnect, socket } = useSocket(SERVER_URL);

  useEffect(() => {
    connect();
    return () => {
      disconnect();
    };
  }, [SERVER_URL]);

  return <ChildComponent />;
};

```

그렇다면 socket 을 자식 컴포넌트에 어떻게 전달하면 좋을까?

props, 전역 상태(redux, recoil 등)로 전달할 수 있을것이다.

전역 상태 라이브러리에서 state 는 기본적으로 readonly 다.  
(불변성 관련 내용은 익히 들어 알고 있을것이다.)

socket-io에서는 소켓 연결시 소켓 객체에 변경이 발생된다.  
그래서, 전역 상태에 저장해서 사용하기엔 불가능하다.

props 를 통해 전달하자.  

```jsx
return <ChildComponent socket={socket} />;
```

부모 요소에 많은 로직이 집중되지 않을까?  
소켓 부분만 로직을 분리해 보자.

```ts
const SocketProvider = ({ children, SERVER_URL }) => {
  const { connect, disconnect, socket } = useSocket(SERVER_URL);

  useEffect(() => {
    connect();
    return () => {
      disconnect();
    };
  }, [SERVER_URL]);

  const childrenWithSocket = useMemo(() => {
    return React.Children.map(children, (child) => {
      if (React.isValidElement(child) && socket) {
        return React.cloneElement(child, { socket });
      } else {
        return <></>;
      }
    });
  }, [children, socket]);

  return <>{childrenWithSocket}</>;
};

export default SocketProvider;
```

socket 을 제공하는 socketProvider 컴포넌트를 만들어 봤다.  
소켓쪽 로직(연결 끊길시 처리, 재연결 등)을 socketProvider에만 집중해 작성할 수 있다.  

더 하위 컴포넌트로 socket 전달은 prop 자체를 다시 전달하면 된다.  

```jsx
const ChildComponent = ({ socket }) => {

  const sendMessage = (message) => {
    if (socket) {
      socket.emit("message", message);
    }
  };

  return (
    <div>
      <button onClick={() => sendMessage("text")}>전송</button>
    </div>
  );
};
```
자식 컴포넌트에서 emit에 직접 접근할 수 있고, socket 객체를 Custom Hook 에 주입해 사용할 수도 있게 된다.  

요즘 드는 생각인데 props drilling은 무조건 나쁜거고, 상태관리 라이브러리를 사용해야 한다는 가스라이팅에 당하고 있었던게 아닌가 생각이 든다.

다음엔 전역 상태관리를 지양하는 관점으로 작성해 봐야겠다.
