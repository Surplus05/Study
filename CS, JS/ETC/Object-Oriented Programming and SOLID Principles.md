# Object-Oriented Programming and SOLID Principles

- Object-Oriented Programming

  객체지향에 대한 내용은 인터넷에 많이 나와있고, 학부때부터 귀에 박히도록 들었다.

  내가 생각하는 객체지향 프로그래밍은 다음과 같다.

  객체라는 기본 단위를 두고, 두 객체간의 상호작용으로 로직을 구성하는 것.

  그래서 그 객체라는 기본 단위는 다음과 같은 특성을 갖는다

  1. 추상화 - 대상을 핵심 행위와 속성으로만 나타내는 것.

  2. 캡슐화 - 정보를 감추는 것.

  3. 다형성 - 상황에 따라 다르게 해석되는 것. (같은 이름이라도 상황에 따라 동작이 달라질 수 있음)

  4. 상속 - 상위 객체의 특성을 하위 객체도 물려받음.

  위에서 설명한 객체지향 프로그래밍을 위한 설계 원칙이 SOLID Principles이다.

* SOLID Principles (객체지향 설계 5원칙)

  1. S (단일 책임 원칙) – 하나의 책임만 져야함.

  2. O (개방 폐쇄 원칙) – 코드 변경없이 확장이 가능해야 함.

  3. L (리스코프 치환 원칙) – 상위 객체를 하위 객체로 치환해도 정상 동작해야 함.

  4. I (인터페이스 분리 원칙) – 인터페이스는 클라이언트 기준으로 분리, 사용하지 않으면 의존하지 않아야 함.

  5. D (의존성 역전 원칙) – 저수준보다 고수준에 의존해야 함. 구현보다는 추상화에 의존.

그러면 하나씩 예제를 통해 살펴 보자.

## Single Responsibility Principle

단일 기능을 수행한다는 내용으로 널리 알려져 있고 예제도 class 기반으로 많이 나와있지만 이를 기반으로는 프론트엔드에서 적용되는 예제를 생각해내기 어려웠다.

프론트엔드에서는 책임(기능)이라는 것을 어떻게 나눌 수 있을까?

큰 범위로는 UI와 로직의 분리 (대표적으로 Container - Presenter) 로 나눌 수 있겠다.

Component : UI

Hooks : Logic

Hooks를 더 세부적으로 분리하면, API나 상태관리 (store) 등으로 더 쪼갤 수 있다.

```ts
import React, { useState, useEffect } from "react";

const PostList = () => {
  const [posts, setPosts] = useState([]);

  const apiUrl = "https://test.com/";

  useEffect(() => {
    const fetchPosts = async () => {
      try {
        const response = await fetch(apiUrl);
        const postData = await response.json();
        setPosts(postData);
      } catch (error) {
        console.error("Error fetching posts:", error);
      }
    };

    fetchPosts();
  }, []);

  return (
    <div>
      <h2>Post List</h2>
      <ul>
        {posts
          .filter((post) => !post.title.includes("필터링"))
          .map((post) => (
            <li key={post.id}>
              <h3>{post.title}</h3>
              <p>{post.body}</p>
            </li>
          ))}
      </ul>
    </div>
  );
};

export default PostList;
```

위 컴포넌트에서의 책임은 Display, Fetch, Filter 으로 볼 수 있다.

```ts
import React, { useState, useEffect } from "react";

const PostList = () => {
  const { filteredPostList } = usePost();

  return (
    <div>
      <h2>Post List</h2>
      <ul>
        <Post postList={filteredPostList} />
      </ul>
    </div>
  );
};

export default PostList;
```

```ts
const Post = ({ postList }) => {
  return postList.map((post) => (
    <li key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.body}</p>
    </li>
  ));
};
```

```ts
const usePost = () => {
  const [postList, setPostList] = useState([]);
  const [isLoading, setIsLoading] = useState(false);

  const apiUrl = "test";

  useEffect(() => {
    const fetchPostData = async () => {
      setIsLoading(true);
      try {
        const response = await fetch(apiUrl);
        const data = await response.json();
        setPostList(data);
      } catch (error) {
        console.log("Error fetching posts:", error);
      } finally {
        setIsLoading(false);
      }
    };

    fetchPostData();
  }, [apiUrl, fetch]);

  const filterPostList = useMemo(
    () => postList.filter((post) => !post.title.includes("필터")),
    [postList]
  );

  return { isLoading, postList, filterPostList };
};
```

usePost를 더 쪼갤수도 있겠으나, 개인적으로는 더 쪼개는 것은 실이 더 많을 것 같다.

## Open Close Principle

다음은 개방 폐쇄 원칙을 준수하는 예제다.

```ts
const useLoginMethod = () => {
  const [loginMethodList, setLoginMethodList] = useState({});

  useEffect(() => {
    const APPLE = useApple();
    const GOOGLE = useGoogle();

    setLoginMethods({ APPLE, GOOGLE });
  }, []);

  return { loginMethodList };
};
```

```ts
const Login = () => {
  const { loginMethodList } = useLoginMethod();
  const { login } = useLogin();

  return <ul>LoginMethodList.map((loginMethod) => {
    return <li key={loginMethod.title} onClick={login(loginMethod)}>loginMethod.title</li>
  })</ul>;
};
```

로그인 방법이 추가되더라도, useLogin 훅은 변화가 필요하지 않다.

컴포넌트 단에서 적용하는 방법은 Children 을 활용하는 예시도 존재한다.

## Liskov Substitution Principle

클래스의 상속과 다형성을 활용하는 예제가 많이 보이는데, 프론트엔드에선 잘 사용하지 않기에 예제를 생각하기 어려웠다.

부모 자식 hook? ..  
동일한 상태를 갖는 부모 자식 component? ..

## Interface Segregation Principle

작성 중 ..

## Dependency Inversion Principle

작성 중 ..
