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

좁은 범위로는 단일 기능을 하도록 코드를 작성하는 것으로 볼 수 있고,
큰 범위로는 UI와 로직의 분리 (대표적으로 Container - Presenter) 로 볼 수 있겠다.

우선 좁은 범위에서의 SRP를 다뤄 보자.

```ts
function emailClients(clients: Client[]) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

메일 전송과 필터링 두 기능을 책임지고 있다.

```ts
function emailClients(clients: Client[]) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client: Client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

단일 책임을 갖도록 분리시켰다.

큰 범위에서의 SRP를 다뤄 보자.

UI와 Logic 으로 나눌 수 있고 Logic을 더 세부적으로 분리하면 Hook, API, 상태관리 (store) 등으로 더 쪼갤 수 있다.

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

다음은 개방 폐쇄 원칙도 한번 살펴 보자.

```ts
function getMutipledArray(array, option) {
  const result = [];
  for (let i = 0; i < array.length; i++) {
    if (option === "doubled") {
      result[i] = array[i] * 2;
    }
    if (option === "tripled") {
      result[i] = array[i] * 3;
    }
    if (option === "half") {
      result[i] = array[i] / 2;
    }
  }
  return result;
}
```

분기 처리가 가능하지만, 수정이 발생한다.

```ts
function map(array, fn) {
  const result = [];
  for (let i = 0; i < array.length; i++) {
    result[i] = fn(array[i], i, array);
  }
  return result;
}

const getDoubledArray = (array) => map(array, (x) => x * 2);
const getTripledArray = (array) => map(array, (x) => x * 3);
const getHalfArray = (array) => map(array, (x) => x / 2);
```

map의 변화 없이 추가가 가능해 졌다.

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

상위 객체와 하위 객체를 치환해도 문제가 없어야 한다.

```ts
interface Post {
  title: string;
  author: string;
  id: number;
}

interface Notice extends Post {}

interface Deal extends Post {}
```

```tsx
const postModify = ({ post }: { post: Post }) => {
  const { editPost, deletePost } = usePost();

  const edit = () => {
    editPost(post);
  };

  const delete = () => {
    deletePost(post)
  }

  return (
    <div>
      <button onClick={edit}>Edit</button>
      <button onClick={delete}>Delete</button>
    </div>
  );
};
```

이런 상황에서 Notice 나 Deal을 보내는 것에 관계없이 제대로 동작해야 한다.

## Interface Segregation Principle

작성 중 ..

## Dependency Inversion Principle

작성 중 ..

- 참고한 곳  
  https://velog.io/@teo/Javascript%EC%97%90%EC%84%9C%EB%8F%84-SOLID-%EC%9B%90%EC%B9%99%EC%9D%B4-%ED%86%B5%ED%95%A0%EA%B9%8C#solid-%EC%9B%90%EC%B9%99%EC%9D%B4%EB%9E%80

  https://kooku0.github.io/blog/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%EC%97%90-solid-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0/#3-solid

  https://fe-developers.kakaoent.com/2023/230330-frontend-solid/
