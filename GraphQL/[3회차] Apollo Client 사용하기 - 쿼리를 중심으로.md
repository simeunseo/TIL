| 작성일     | 회차  |
| ---------- | ----- |
| 2024/05/22 | 3회차 |

# 🍴 Apollo Client vs Relay 비교

---

Apollo Client는 Relay만큼 주목받는 graphQL 클라이언트이다. 간단히 주요 차이점을 살펴보자.

![Untitled](https://file.notion.so/f/f/2896369f-ef60-4b3e-85c4-25d1f47c8cd9/b3d24ccd-fa74-41b5-9bca-73b1732460a0/Untitled.png?id=73cb286a-eea9-41cf-9bdd-773a781e205b&table=block&spaceId=2896369f-ef60-4b3e-85c4-25d1f47c8cd9&expirationTimestamp=1716465600000&signature=l_83HZsH5EHCt06tay4Z7up1vFzoM4J0dF-3wipBFDU&downloadName=Untitled.png)

### Apollo Client

`#flexible` `#easygoing`

유연성이 요구되고 다양한 개발환경을 가진 프로젝트에 적합하다.

**장점**

- React, Angular, Vue 등 거의 모든 환경에서 사용할 수 있다.
- 자동 및 수동 캐시 업데이트를 지원하여 성능 최적화에 유리하다. 따라서 사용자 정의 캐시 로직이 필요할 경우 Apollo가 크게 도움이 될 수 있다.

**단점**

- 라이브러리 크기가 Relay보다 다소 크다.

### Relay

`#structured` `#opinionated`

복잡한 데이터 요구사항을 가진 큰 규모의 프로젝트에 적합하다.

**장점**

- 클라이언트와 서버 간의 요청을 최적화하여 불필요한 데이터 전송을 최소화한다.
- GraphQL 스키마와 타이트하게 통합되어 타입 안정성을 보장한다.

**단점**

- React와 React Native에서 밖에 사용하지 못한다
- Apollo에 비해 유연성이 떨어진다.

> 이제 공식문서를 기반으로 Apollo Client를 직접 사용해보자!

# 🏁 Apollo Client 시작하기

---

## apollo client, graphql 설치

```bash
yarn add @apollo/client graphql
```

## 초기 설정

### **1. apollo client 인스턴스 생성**

```jsx
const client = new ApolloClient({
  uri: "https://flyby-router-demo.herokuapp.com/",
  cache: new InMemoryCache(),
});
```

- `uri` : GraphQL 서버 URL이다.
- `cache`: 쿼리 결과를 캐싱하는 InMemoryCache의 인스턴스이다.

**데이터 페칭을 테스트 해보면…**

```jsx
const GET_LOCATIONS = gql`
  query GetLocations {
    locations {
      id
      name
      description
      photo
    }
  }
`;

client
  .query({
    query: GET_LOCATIONS,
  })
  .then((res) => console.log(res));
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/2896369f-ef60-4b3e-85c4-25d1f47c8cd9/2b4b6108-7627-4d1a-8417-a5b508a066f8/Untitled.png)

요청한 `data`, `loading`, `networkStatus` 속성이 가져와졌다.

### 2. React와 연결하기 (Provider 삽입)

```jsx
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>,
```

### 3. useQuery로 데이터 페치하기

```jsx
import { gql, useQuery } from "@apollo/client";

function DisplayLocations() {
  const GET_LOCATIONS = gql`
    query GetLocations {
      locations {
        id
        name
        description
        photo
      }
    }
  `;

  const { loading, error, data } = useQuery(GET_LOCATIONS);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return data.locations.map(({ id, name, description, photo }) => (
    <div key={id}>
      <h3>{name}</h3>
      <img width="400" height="250" alt="location-reference" src={`${photo}`} />
      <br />
      <b>About this location:</b>
      <p>{description}</p>
      <br />
    </div>
  ));
}

export default DisplayLocations;
```

`useQuery` hook의 반환값으로 `data`, `loading`, `error`를 받아올 수 있다.

- 이 외에도 반환값에는 다음과 같은 정보들이 들어있다.
  ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/2896369f-ef60-4b3e-85c4-25d1f47c8cd9/8cea2ccd-cfe0-4810-a2d9-f1ee2eb7b3de/Untitled.png)

# 🤝 useQuery 더 깊게 알기

---

## 캐싱

Apollo Client는 쿼리를 페치한 후 그 결과를 자동으로 캐시한다.

```jsx
const GET_DOG_PHOTO = gql`
  query Dog($breed: String!) {
    dog(breed: $breed) {
      id
      displayImage
    }
  }
`;

function DogPhoto({ breed }) {
  const { loading, error, data } = useQuery(GET_DOG_PHOTO, {
    variables: { breed },
  });

  if (loading) return null;
  if (error) return `Error! ${error}`;

  return (
    <img src={data.dog.displayImage} style={{ height: 100, width: 100 }} />
  );
}
```

`useQuery` hook에 `variables` 옵션을 넘길 수 있다. `variables`는 객체 형태로 전달하며, 쿼리에 필요한 변수이다. 이 예제에서는 드롭다운을 통해 선택한 특정 `breed`에 대한 데이터를 페칭하기 때문에, 해당 `bread`에 대한 쿼리를 캐싱하기 위해 `breed` 값을 전달한다.

## 캐싱된 결과 업데이트하기

캐시된 데이터를 서버의 최신 데이터와 일치시키고 싶을 때, Apollo Client에서는 `polling`과 `refetching`이라는 두 가지 전략을 사용할 수 있다.

### polling

polling은 지정한 간격마다 주기적으로 쿼리를 다시 실행해서 서버 데이터와 동기화하는 기능이다. `useQuery` hook에 `pollInterval` 옵션(밀리초 단위)을 넘겨 설정할 수 있다.

```jsx
const { loading, error, data } = useQuery(GET_DOG_PHOTO, {
  variables: { breed },
  pollInterval: 500, // 현재 선택된 breed에 대한 데이터를 0.5초마다 가져옴
  // 0으로 설정하면 polling하지 않음
});
```

`startPolling`과 `stopPolling` 함수를 통해 polling을 동적으로 시작하고 중지할 수도 있다! [참고](https://www.apollographql.com/docs/react/api/react/hoc/#datastartpollinginterval)

### refetching

refetching은 고정된 간격이 아니라, 특정 사용자 동작에 응답해서 쿼리를 다시 실행하는 기능이다.

```jsx
function DogPhoto({ breed }) {
  const { loading, error, data, refetch } = useQuery(GET_DOG_PHOTO, {
    variables: { breed },
  });

	...

  return (
    <div>
      <img src={data.dog.displayImage} style={{ height: 100, width: 100 }} />
      <button onClick={() => refetch()}>
        Refetch new breed!
      </button>
    </div>
  );
}
```

위와 같이 버튼을 클릭했을 때 `refetch()`를 실행하여 쿼리를 다시 가져올 수 있다.

```jsx
<button
  onClick={() =>
    refetch({
      breed: "dalmatian",
    })
  }
>
  Refetch!
</button>
```

`refetch` 함수에 `variables`를 넘길 수도 있는데, 위와 같이 `breed`를 ‘dalmatian’으로 넘기면 항상 달마시안 데이터를 가져오게 된다. `variables`를 생략하면 이전 실행에서와 동일한 변수를 사용한다.

## 로딩 상태 확인하기

`useQuery`의 반환값 중 하나인 `loading`으로 로딩 상태를 확인할 수 있지만, `refetch`할 때는 로딩 상태를 알 수 없다. 이 때 `networkStatus` 속성을 통해 쿼리 상태에 대한 세부적인 정보를 확인할 수 있다.

```jsx
const { loading, error, data, refetch, networkStatus } = useQuery(
  GET_DOG_PHOTO,
  {
    variables: { breed },
    notifyOnNetworkStatusChange: true,
  }
);
```

`networkStatus` 속성을 사용하려면, 위와 같이 `notifyOnNetworkStatusChange`값을 `true`로 설정해주어야 한다. `networkStatus` 속성은 다양한 로딩 상태를 나타내는 `enum`이며, `refetch` 상태에 대해서는 `networkStatus.refetch`와 같이 확인할 수 있다.

```jsx
if (networkStatus === NetworkStatus.refetch) return "Refetching...";
```

## useLazyQuery로 쿼리를 수동 실행하기

기본적으로 `useQuery`는 `useQuery`를 호출하는 컴포넌트를 렌더링할 때 쿼리를 자동으로 실행한다. 그러나 특정 이벤트에 응답하여 쿼리를 실행하고 싶을 수 있다. 이 때 `useLazyQuery` hook을 사용하여 수동적으로 쿼리를 호출할 수 있다.

```jsx
function DelayedQuery() {
  const [getDog, { loading, error, data }] = useLazyQuery(GET_DOG_PHOTO);

  if (loading) return <p>Loading ...</p>;
  if (error) return `Error! ${error}`;

  return (
    <div>
      {data?.dog && <img src={data.dog.displayImage} />}
      <button onClick={() => getDog({ variables: { breed: "bulldog" } })}>
        Click me!
      </button>
    </div>
  );
}
```

`useLazyQuery`는 `useQuery`와 다르게 쿼리 함수를 반환한다. (getDog) 이 쿼리 함수를 사용하여 원하는 때에 쿼리를 실행할 수 있다.

## 페치 정책 설정하기

`useQuery` hook은 기본적으로 Apollo Client 캐시를 확인하여, 요청한 모든 데이터가 로컬에 이미 있는지 확인한다. 있다면 `useQuery`는 그 데이터를 반환하고 GraphQL 서버에 쿼리를 보내지 않는다. 이것을 `cache-first` 정책이라고 하며, Apollo Client의 기본 페치 정책이다.

### 페치 정책의 종류

| cache-first (기본) | 먼저 캐시에 대해 쿼리를 실행한다. 요청한 모든 데이터가 캐시에 존재한다면 해당 데이터를 반환하고, 그렇지 않으면 GraphQL 서버에 대해 쿼리를 실행하고 해당 데이터를 캐싱한 후 반환한다.                              |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| cache-only         | 쿼리를 오직 캐시에 대해서만 실행한다. 이 경우 서버에 쿼리를 보내지 않으며, 요청한 모든 필드에 대한 데이터가 캐시에 없는 경우 오류가 발생한다.                                                                     |
| cache-and-network  | 쿼리를 캐시와 GraphQL 서버 모두에 대해 실행한다. 서버 쪽 쿼리 결과가 캐시된 필드를 수정하게 된다면 쿼리가 자동으로 업데이트된다. 빠른 응답을 제공하면서도 캐시된 데이터를 서버 데이터와 동기화하도록 도움을 준다. |
| network-only       | 먼저 캐시를 확인하지 않고 전체 쿼리를 GraphQL 서버에 대해 실행한다. 쿼리 결과는 캐시에 저장된다. 서버 데이터와 일관성을 유지할 수 있지만 캐시된 데이터가 있을 때 즉각적인 응답을 제공할 수는 없다.                |
| no-cache           | network-only와 유사하지만, 쿼리 결과가 캐시에 저장되지 않는다.                                                                                                                                                    |
| standby            | cache-first와 유사하지만, 이 쿼리는 기본 필드 값이 변경될 때 자동으로 업데이트되지 않는다. refetch 및 updateQueries를 사용하여 수동으로 쿼리를 업데이트 할 수 있다.                                               |

### 페치 정책 설정하기

`useQuery` hook에 `fetchPolicy` 옵션을 전달하여 설정할 수 있다.

```jsx
const { loading, error, data } = useQuery(GET_DOGS, {
  fetchPolicy: "network-only", // 캐시를 확인하지 않고 네트워크 요청을 보냄
});
```

### nextFetchPolicy

`nextFetchPolicy`를 지정하면, `fetchPolicy`는 쿼리의 첫 번째 실행에 사용되고 이후 캐시 업데이트에 대해서는 `netFetchPolicy`를 사용한다.

```jsx
const { loading, error, data } = useQuery(GET_DOGS, {
  fetchPolicy: "network-only", // 첫 번째 실행에 사용
  nextFetchPolicy: "cache-first", // 이후 실행에 사용
});
```

이러한 설정을 ApolloClient 인스턴스에 대해 일관적으로 적용하고 싶다면 다음과 같이 `defaultOptions`를 설정할 수 있다.

```jsx
new ApolloClient({
  link,
  cache: new InMemoryCache(),
  defaultOptions: {
    watchQuery: {
      nextFetchPolicy: "cache-only",
    },
  },
});
```

# Mutations

`useMutation`으로 뮤테이션을 실행할 수 있다.

```jsx
import { gql, useMutation } from "@apollo/client";

const INCREMENT_COUNTER = gql`
  mutation IncrementCounter {
    currentValue
  }
`;

function MyComponent() {
  const [mutateFunction, { data, loading, error }] =
    useMutation(INCREMENT_COUNTER);
}
```

useMutation의 반환값에는 mutation 함수가 포함된다. `useQuery`와 달리 `useMutation`은 컴포넌트 렌더 시 자동으로 작업을 실행하지 않는데, 대신 이 `mutate` 함수를 호출해야 한다.

### 옵션 전달하기

`useMutation` hook에 옵션 객체를 전달하여 GraphQL 변수에 대한 기본값을 제공할 수 있다.

```jsx
const [addTodo, { data, loading, error }] = useMutation(ADD_TODO, {
  variables: {
    type: "placeholder",
    someOtherVariable: 1234,
  },
});
```

`mutate` 함수에 직접 옵션을 전달할 수도 있다.

```jsx
addTodo({
  variables: {
    type: input.value,
  },
});
```
