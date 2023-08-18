# Client State, Server State  

* Client State  
  주로 서버와 관련없는 테마, 언어등의 데이터를 의미.

* Server State  
  게시글 정보, 유저 정보 등 서버에 저장되는 데이터를 의미.

# React Query
리액트 쿼리는 Server State를 효율적으로 관리하도록 여러 기능을 제공하는 라이브러리.

# Queries  

* unique key (query key)  
각 쿼리는 string 타입으로 선언된 unique key 와 의존관계에 있다.  
고유한 key를 설정해주어야 한다.
만약 query Fundtion이 변수에 의존하는 경우 query key 에 포함시킬 수 있다.

``` typescript
function Todos({ todoId }) {
  const result = useQuery({
    queryKey: ['todos', todoId],
    queryFn: () => fetchTodoById(todoId),
  })
}
```

useQuery 훅을 통해 데이터를 subscribe 할 수 있다.  
이때, unique key 와 query function이 필요하다.  
설정한 unique key는 내부에서 refetching, caching, 그리고 어플리케이션 내부에서 쿼리를 식별할때 사용된다.  
queryFunction은 promise를 리턴해야 하고, useQuery의 결과로 resolved date 또는 error값이 반환된다.  

* query function  
  query function 은 promise를 리턴해야 한다.
  이 리턴된 promise는 data를 resolve 하거나 또는 error 를 throw 해야 한다.

  axios, graphql-request는 실패 코드가 를 응답받으면 자동으로 에러를 던져주지만, fetch같은 일부 유틸리티는 그렇지 않다.
  그래서 후자를 택하는 경우는 명시적으로 에러를 던져주어야 한다.

  ``` typescript
  useQuery({
  queryKey: ['todos', todoId],
  queryFn: async () => {
    const response = await fetch('/todos/' + todoId)
    if (!response.ok) {
      throw new Error('Network response was not ok')
    }
    return response.json()
  },
  })
  ```
  그리고, query key 는 fetching data의 유일한 식별자뿐만 아니라 QueryFunctionContext의 일부로서 query function으로 편리하게 전달된다.
  ``` typescript
  function Todos({ status, page }) {
  const result = useQuery({
    queryKey: ['todos', { status, page }],
    queryFn: fetchTodoList,
  })
  }  

  // Access the key, status and page variables in your query function!
  function fetchTodoList({ queryKey }) {
    const [_key, { status, page }] = queryKey
    return new Promise()
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

* useQueries
  동적 병렬 쿼리에 사용된다.
  ``` typescript
  function App({ users }) {
  const userQueries = useQueries({
    queries: users.map((user) => {
      return {
        queryKey: ['user', user.id],
        queryFn: () => fetchUserById(user.id),
      }
    }),
  })
  }
  ```

* Dependent Query
  특정 값에 의존하는 쿼리를 만들때 사용된다.
  enabled 옵션으로 쉽게 사용할 수 있다.
  ``` typescript
  // Get the user
  const { data: user } = useQuery({
    queryKey: ['user', email],
    queryFn: getUserByEmail,
  })
  
  const userId = user?.id
  
  // Then get the user's projects
  const {
    status,
    fetchStatus,
    data: projects,
  } = useQuery({
    queryKey: ['projects', userId],
    queryFn: getProjectsByUser,
    // The query will not execute until the userId exists
    enabled: !!userId,
  })
  ```

  간단하게 다루어 봤다.
  다른 기능들과 자세한 내용은 [공식문서](https://tanstack.com/query/latest/docs/react/overview) 에서 확인 해 보자.

