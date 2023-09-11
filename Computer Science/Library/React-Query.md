# React-Query

data Fetching 해서 export 해주는 hook을 생각 해 보자.  
2개의 컴포넌트가 각각 hook에서 fetching이 2번 이루어진다.  
hook은 값의 재사용이 아닌, 로직의 재사용을 위함이다.  
그래서, fetching 한 값을 관리해주지는 않는다.  
데이터를 재사용하기 위해서는, 전역변수를 사용하거나 별도의 방법을 사용해야 한다.

React-Query는 이러한 문제들을 해결 해 주는데, Cache와 Retry 등의 기능을 제공한다.

단순한 비동기 상태관리 뿐 만 아니라 data fetching 로직을 직접 작성할 필요 없이 어디서 가져올지, cache 얼마정도 유지할지만 알려주면 알아서 해 준다고 한다.

React-Query 는 리액트 data-fetching 라이브러리로 설명되곤 하지만, fetching, caching, synchronizing and updating server state 를 가능하게 해 준다.

# Client State, Server State

- Client State  
  주로 서버와 관련없는 테마, 언어등의 데이터를 의미.

- Server State  
  게시글 정보, 유저 정보 등 서버에 저장되는 데이터를 의미.

# React Query

리액트 쿼리는 Server State를 효율적으로 관리하도록 여러 기능을 제공하는 라이브러리.

전용 개발툴을 제공하므로 같이 설치해서 사용하자.

- 주의점  
  튜토리얼에 기본적으로 key만 사용하면 함정에 빠지기 쉽다.  
  토글, 윈도우 포커스등 이벤트에서 계속 새로운 fetching이 발생하게 된다.

* Defaults  
  useQuery, useInfiniteQuery 를 통한 Query Instance는 기본적으로 stale 으로 간주된다.

  stale 쿼리들은 다음 상황에 백그라운드에서 자동적으로 re-fetch된다.

  새 쿼리 생성시  
  윈도우 포커스시  
  네트워크 연결시  
  re-fetch interval 설정시

  re-fetch 를 막기 위해서는 staleTime, refetchOnMount, refetchOnWindowFocus 등 설정을 만져주어야 한다.

  활성화 된 쿼리 옵저버들이 없다면, 기본적으로 5분이 지나면 가비지 컬렉션이 실행된다.

  이를 위해서는 cacheTime 또한 설정해주어야 한다.

  fetch 실패시에는 기본적으로 3번 재시도하는데, 이 또한 조정이 가능하다.

  요약하자면, 튜토리얼만 따라하게 된다면 위와 같은 re-fetch 함정에 빠질 수 있다. 유의해서 사용하자.

# Queries, Query Keys

Query Key는 배열로 설정할 수 있는데, 이를 통해 더 세밀하게 조합할 수 있다.

- unique key (query key)  
  각 쿼리는 unique key 와 의존관계에 있다.  
  고유한 key를 설정해주어야 한다.
  만약 query Function이 변수에 의존하는 경우 query key 에 포함시킬 수 있다.

  그리고, ["todos", todoId] 가 쿼리키인 경우, ["todos"] 를 쿼리키로 주면 모든 todoId를 포함하는 쿼리키가 된다.

```typescript
function Todos({ todoId }) {
  const result = useQuery({
    queryKey: ["todos", todoId],
    queryFn: () => fetchTodoById(todoId),
  });
}
```

useQuery 훅을 통해 데이터를 subscribe 할 수 있다.  
이때, unique key 와 query function이 필요하다.  
설정한 unique key는 내부에서 refetching, caching, 그리고 어플리케이션 내부에서 쿼리를 식별할때 사용된다.  
queryFunction은 promise를 리턴해야 하고, useQuery의 결과로 resolved date 또는 error값이 반환된다.

- query function  
  query function 은 promise를 리턴해야 한다.
  이 리턴된 promise는 data를 resolve 하거나 또는 error 를 throw 해야 한다.

  axios, graphql-request는 실패 코드가 를 응답받으면 자동으로 에러를 던져주지만, fetch같은 일부 유틸리티는 그렇지 않다.
  그래서 후자를 택하는 경우는 명시적으로 에러를 던져주어야 한다.

  ```typescript
  useQuery({
    queryKey: ["todos", todoId],
    queryFn: async () => {
      const response = await fetch("/todos/" + todoId);
      if (!response.ok) {
        throw new Error("Network response was not ok");
      }
      return response.json();
    },
  });
  ```

  그리고, query key 는 fetching data의 유일한 식별자뿐만 아니라 QueryFunctionContext의 일부로서 query function으로 편리하게 전달된다.

  ```typescript
  function Todos({ status, page }) {
    const result = useQuery({
      queryKey: ["todos", { status, page }],
      queryFn: fetchTodoList,
    });
  }

  // Access the key, status and page variables in your query function!
  function fetchTodoList({ queryKey }) {
    const [_key, { status, page }] = queryKey;
    return new Promise();
  }
  ```

  queryKey 프로퍼티를 받으면

반환하는 객체에는 isLoading, isError, isSuccess, error, data, status, fetchStatus등 이 있다.

isLoading 은 아직 데이터가 없다는 것을 나타낸다.  
isError는 에러를 만난 경우를 나타낸다.  
isSuccess는 쿼리가 성공했고 데이터가 사용가능함을 나타낸다.

isError이 true라면 error에 값이 있을것이고, isSuccess가 true라면 data에 값이 있을것이다.

status는 boolean타입의 상태를 string형태로 제공한다.  
isloading 이 true면 status 는 'loading', isError가 true라면 status 는 'error' 이런식이다.

fetchStatus는 fetching, paused, idle이 될 수 있는데, 두 state가 나뉘는 이유는 backgorund refetch, stale-while-revalidate 로직등을 status와 fetchStatus 조합을 통해 구성이 가능하기 때문이다.

예를들어, success와 fetching을 조합하면, 이미 성공된 쿼리에 refetch가 이루어지는 중임을 알 수 있다.  
또, loading과 fetching은 fetch가 이루어지는 중임을 알 수 있고 loading과 idle은 네트워크 연결이 없음을 알 수 있다.

- useQueries
  동적 병렬 쿼리에 사용된다.

  ```typescript
  function App({ users }) {
    const userQueries = useQueries({
      queries: users.map((user) => {
        return {
          queryKey: ["user", user.id],
          queryFn: () => fetchUserById(user.id),
        };
      }),
    });
  }
  ```

- Dependent Query
  특정 값에 의존하는 쿼리를 만들때 사용된다.
  enabled 옵션으로 쉽게 사용할 수 있다.

  ```typescript
  // Get the user
  const { data: user } = useQuery({
    queryKey: ["user", email],
    queryFn: getUserByEmail,
  });

  const userId = user?.id;

  // Then get the user's projects
  const {
    status,
    fetchStatus,
    data: projects,
  } = useQuery({
    queryKey: ["projects", userId],
    queryFn: getProjectsByUser,
    // The query will not execute until the userId exists
    enabled: !!userId,
  });
  ```

  간단하게 다루어 봤다.
  다른 기능들과 자세한 내용은 [공식문서](https://tanstack.com/query/latest/docs/react/overview) 에서 확인 해 보자.
