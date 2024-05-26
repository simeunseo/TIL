| 작성일     | 회차  |
| ---------- | ----- |
| 2024/04/29 | 1회차 |

# 🤔 GraphQL이 뭘까?

---

GraphQL은 페이스북에서 만든 쿼리 언어로, API로부터 필요한 데이터의 구조를 정의하는 타입 시스템을 사용하여 쿼리를 실행한다. 각 타입은 자체 필드를 가지며 서로 관계를 통해 연결되어, 단일 API 호출 안에서 다양한 엔티티에 걸친 복잡한 쿼리를 가능하게 한다.

## 주요 특징

- **타입 시스템**
  강력한 타입 시스템을 사용하여, API를 통해 쿼리할 수 있는 데이터의 종류를 명확히 정의한다. 이 시스템은 데이터의 구조와 가능한 쿼리를 사전에 알 수 있게 해주어, 효육적인 작업을 돕는다.
- **단일 엔드포인트**
  여러 엔드포인트를 관리할 필요 없이 단일 엔드포인트에서 데이터의 모든 기능에 접근할 수 있어 API 관리를 단순화시킨다.
- **실시간 데이터**
  subscription 기능을 통해 실시간으로 데이터를 받아올 수 있어, 실시간 이벤트를 효과적으로 다룰 수 있게 한다.
- **자가 문서화**
  API의 스키마를 사용하여 API 자체가 자신의 구조와 사용 가능한 연산을 설명할 수 있도록 한다. 따라서 별도의 문서를 업데이트하거나 유지할 필요 없이 API의 최신 상태를 정확하게 반영할 수 있게 한다.

## 사용 동향

국내에서는 다음과 같은 기업들이 GraphQL을 사용하고 있다.

![Untitled](https://file.notion.so/f/f/2896369f-ef60-4b3e-85c4-25d1f47c8cd9/26903742-a0cc-4646-88eb-5bbf497a4d68/Untitled.png?id=1b766c76-3fdc-40c4-bd85-5bb3c973fa24&table=block&spaceId=2896369f-ef60-4b3e-85c4-25d1f47c8cd9&expirationTimestamp=1716465600000&signature=sI9m9OLEW7JG709LxF-Jh6-Ht0wvIyLC4Txm1b26aRA&downloadName=Untitled.png)

# 🫧 REST API와 비교해보자

---

<aside>
🗒️ REST API는 REST를 기반으로 만들어진 API이다. REST란 HTTP URI를 통해 자원을 명시하고, HTTP Method(POST, GET, PUT, DELETE, PATCH)를 통해 해당 자원에 대한 CRUD Operation을 적용하는 설계 방식이다.

</aside>

![Untitled](https://file.notion.so/f/f/2896369f-ef60-4b3e-85c4-25d1f47c8cd9/ff4c8086-296d-41f0-b2dd-e55ed2867379/Untitled.png?id=0cacdcdc-f3c8-4cdc-9bb2-0a68f1833cd3&table=block&spaceId=2896369f-ef60-4b3e-85c4-25d1f47c8cd9&expirationTimestamp=1716465600000&signature=XJxEh2F6BfB2BKJD65pnOVDrOZ9vsUTaMmbTmEo2H5s&downloadName=Untitled.png)

## REST API의 문제점과 GraphQL의 보완

- **오버 페칭과 언더 페칭**
  REST API는 특정 엔드포인트에서 고정된 데이터 구조를 제공한다. 이로 인해 필요한 정보보다 많은 정보를 가져오는 오버 페칭이나, 한 번의 요청으로 필요한 모든 정보를 가져오지 못하는 언더 페칭이 발생할 수 있다.
  > → GraphQL에서는 클라이언트가 필요한 데이터만 정확하게 요청할 수 있어서 문제를 해결할 수 있다!
- **버전 관리**
  REST API에서 새로운 필드를 추가하거나 기존 필드를 변경할 때, 이전 버전과의 호환성을 유지하기 위해 새로운 버전의 API를 배포해야 할 수 있다. 이러한 버전 관리는 복잡성을 증가시키고 다양한 버전을 유지 관리해야 하는 부담을 가져온다.
  > → GraphQL에서는 단일 엔드포인트를 통해 타입 시스템 내에서 점진적인 변경을 관리할 수 있어서 버전 관리의 필요성이 줄어든다!
- **타입 시스템의 부재**
  REST API는 타입 시스템이 없어 데이터의 구조를 명시적으로 정의하지 않는다. 이로인해 클라이언트와 서버 간의 약속을 명확히 하는데 어려움이 있을 수 있다.
  > → GraphQL은 타입 시스템을 사용하여 교환되는 데이터의 구조와 타입을 명확하게 정의하고 검증할 수 있어 오류를 방지할 수 있다.
- **복잡한 연관 데이터 처리**
  REST API는 관련 데이터나 연관된 데이터를 다루기 위해 여러 개의 API를 호출해야 할 때가 많다.
  > → GraphQL은 하나의 쿼리 안에서 여러 자원과 그 관계를 효율적으로 받아올 수 있어, API 호출 수를 줄이고 성능을 향상시킬 수 있다.

## GraphQL의 단점

- **에러 핸들링의 어려움**
  REST API는 HTTP 상태 코드를 사용하여 오류의 성격을 나타낸다. 그러나 GraphQL은 일반적으로 상태 코드 200을 반환하며, 성공한 쿼리는 data에 필드에 담기고 실패한 쿼리는 errors 필드에 배열로 담긴다. error 배열의 객체들은 각 쿼리 필드에 대한 실패 사유와 위치 정보를 담고 있는데, 각각에 대한 에러 여부를 파악하고 대응해야 하므로 매우 까다롭다.
  > Apollo Client 라이브러리를 사용하면 에러를 중간에 가로챌 수 있고, 렌더링 로직과 분리하여 비교적 깔끔하게 핸들링할 수 있다.
- **캐싱의 부재**
  REST API는 여러 엔드포인트를 가지므로 HTTP 캐싱을 활용할 수 있지만 GraphQL은 그렇지 않으므로 캐싱의 이점을 누릴 수 없다.
  > Apollo Client 라이브러리를 사용하면 캐시 옵션을 사용할 수 있다.
- **복잡성 증가**
  GraphQL은 백엔드 개발의 측면에서 REST API보다 더 복잡한 쿼리와 스키마 설계를 필요로 한다. 자유로운 쿼리 조합에 대해 대응해야 하며 서버 부담을 줄이기 위해 쿼리의 최대 개수나 뎁스를 제한하는 것을 고려하는 등 API 설계 시 고민할 지점들이 많아진다.
  > 클라이언트 로직은 간결해지는 이점이 있다.

# 🫣 GraphQL 사용법 맛보기

---

## 기본 구성 요소

- **쿼리(Queries)**
  데이터를 읽을 때 사용한다. REST API의 GET 요청과 유사하다.
- **뮤테이션(Mutations)**
  데이터를 생성, 수정, 삭제할 때 사용한다. REST API의 POST, PUT, DELETE 요청과 유사하다.
- **스키마(Schema)**
  가능한 쿼리와 해당 쿼리가 반환할 객체 타입을 정의한다.
- **리졸버(Resolvers)**
  쿼리에 대한 응답을 실제 데이터로 채우는 함수이다.

## 쿼리 작성 예시

```graphql
{
  hero {
    name
    height
    mass
    friends {
      name
    }
  }
}
```

`hero`라는 주 객체에 대해, `hero`의 속성인 `name`, `height`, `mass`, `friends`를 요청한다. `friends`의 속성인 name도 요청한다.

### 필드

GraphQL은 객체에 대한 특정 필드를 유연하게 요청할 수 있다. 같은 hero 객체에 대해, 필요한 정보에 따라 다음과 같이 다르게 요청하여 결과를 얻을 수 있다.

```graphql
//쿼리
{
	hero {
		name
	}
}
```

```json
//결과
{
  "data": {
    "hero": {
      "name": "R2-D2"
    }
  }
}
```

```graphql
//쿼리
{
  hero {
    name
    friends {
      name
    }
  }
}
```

```json
//결과
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

### 파라미터

쿼리에서 필드는 파라미터를 받을 수 있다. 이 파라미터들은 필드가 데이터를 반환할 때 어떤 데이터를 반환할지를 제어하는 데 사용된다. 파라미터의 타입이나 기본값을 지정하는 것도 가능하다.

```graphql
{
  user(id: "1") {
    name
  }
}
```

이 예시에서는 `id`를 `user`필드에 파라미터로 전달하여 특정 사용자의 이름을 요청할 수 있다.

```graphql
{
  users(filter: { age: 25, active: true }) {
    name
    email
  }
}
```

파라미터를 활용하여 리스트나 복잡한 객체를 필터링하는 등 복잡한 데이터 요청도 가능하다. 이 예시에서는 특정 조건을 만족하는 사용자에 대한 정보를 요청할 수 있다.

## 뮤테이션 작성 예시

```graphql
mutation {
  addHero(name: "Eunseo Sim", height: 162, mass: 100) {
    id
  }
}
```

새로운 `hero`객체를 추가하고, 추가된 `hero`의 `id`를 반환한다. `name`, `height`, `mass`를 입력 파라미터로 제공한다.

## 스키마 작성 예시

```graphql
type Hero {
  id: ID
  name: String
  height: Int
  mass: Int
  friends: [Hero]
}

type Query {
  hero(id: ID): Hero
}

type Mutation {
  addHero(name: String, height: Int, mass: Int): Hero
}
```

스키마로 서버에서 사용 가능한 타입과 쿼리 구조를 정의한다. 이 스키마는 `Hero` 타입을 정의하고 `Query` 타입에서는 특정 `ID`를 가진 `hero`를 요청할 수 있으며, Mutation 타입에서는 새로운 `hero`를 추가할 수 있다.
