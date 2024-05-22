| 작성일       | 회차  |
| ------------ | ----- |
| May 13, 2024 | 2회차 |

# 🤔 Relay가 뭘까?

---

Relay는 왜 필요할까? GraphQL은 데이터를 주고받는 ‘명세’, ‘형식’이다. 물론 `fetch()`만을 이용해 GraphQL을 기반으로 통신할 수도 있지만, 여러 이점(local state 관리, 데이터 캐싱, 편리한 작성 등)을 위해 GraphQL을 위한 클라이언트 라이브러리를 사용할 수 있다. 그 중 하나가 바로 Relay이다.

Relay의 주요 특징 중 하나는 각 컴포넌트가 자신의 데이터 요구사항을 로컬에서 선언할 수 있게 하면서, 이 요구사항들을 큰 쿼리로 결합하여 성능을 유지할 수 있게 하는 것이다. 따라서 각각의 컴포넌트가 필요로 하는 데이터만을 정확히 가져올 수 있게 돕고, 컴포넌트와 데이터 종속성을 손쉽게 관리할 수 있도록 한다.

## 공식문서에서 소개하는 Relay

**Keeps iteration quick**

Relay는 데이터 페칭을 선언적으로 변환한다. 컴포넌트가 자신의 데이터 의존성을 선언하면, Relay는 각 컴포넌트가 필요로 하는 데이터가 가져와지고 사용 가능하다는 것을 보장한다. 이 덕분에 컴포넌트를 독립적으로 유지하고 재사용이 용이해진다. Relay를 사용하면 컴포넌트와 데이터 의존성을 빠르게 수정할 수 있다.

**Automatic optimizations**

Relay의 컴파일러는 앱 전체의 데이터 요구사항을 최적화하여 단일 GraphQL 요청으로 효율적으로 데이터를 가져오도록 한다. 중복되는 필드를 제거하고, 런타임에 사용되는 정보를 미리 계산하는 등, 컴포넌트에서 선언된 데이터가 가장 효율적으로 페칭되도록 돕는다.

**Data consistency**

Relay는 데이터가 변경될 때 영향을 받는 모든 컴포넌트를 자동으로 최신 상태로 유지하며, 필요할 때만 효율적으로 업데이트한다. 즉, 불필요한 리렌더링이 일어나지 않도록 돕는다. 선택적으로 낙관적 업데이트 및 로컬 데이터 업데이트를 하면서 화면에 표시된 데이터가 항상 최신 상태로 유지되도록 한다.

## Apollo vs Relay 비교해보기

Apollo는 Relay만큼 주목받는 graphQL 클라이언트이다. 간단히 주요 차이점을 살펴보자.

### Apollo

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

# 🤐 Collocation of Fragments

---

## 일반적인 데이터 페칭 방식의 문제점

블로그 포스트 리스트를 보여주고, 각 블로그마다 댓글 리스트가 있는 페이지를 만든다고 가정해보자. 다음과 같이 구현할 수 있을 것이다.

```jsx
// pages/index.tsx
export default function Home({ posts }: { posts: Post[] }) {
  return (
    <div>
      {posts.map((post) => (
        <BlogPost key={post.id} post={post} />
      ))}
    </div>
  );
}
```

```jsx
// components/BlogPost.tsx
export default function BlogPost({ post }: { post: Post }) {
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
      <div>
        {post.comments.map((comment) => (
          <Comment key={comment.id} comment={comment} />
        ))}
      </div>
    </div>
  );
}
```

```jsx
// components/Comment.tsx
export default function Comment({ comment }: { comment: Comment }) {
  return (
    <div>
      <h2>{comment.title}</h2>
      <p>{comment.content}</p>
    </div>
  );
}
```

이 코드처럼, 최상위 컴포넌트에서 데이터를 가져와서 하위 컴포넌트로 내려주는 패턴은 일반적이다.

이렇게 구현했을 때 문제점은 무엇일까? 만약 `Comment` 컴포넌트가 앱에서 10가지 페이지에 사용되고 있고, `Comment` 컴포넌트에 `author`라는 새로운 필드가 필요해졌다고 해보자. 그러면 모든 `Comment` 컴포넌트를 찾아다니며 부모 컴포넌트로 거슬러 올라가 데이터 페칭 과정을 수정을 해야할 것이다. 유지보수가 힘들어지는 것이다. 이 문제는 데이터 페칭을 탑다운으로 설계함으로써, 데이터 페칭 로직과 컴포넌트가 긴밀하게 엮여있기 때문에 발생한다.

## Fragment Collocation 개념 이해하기

Fragment Collocation은 GraphQL fragment를 다른 별도의 파일이 아닌 컴포넌트 안에 직접 선언하는 것이다. 일일이 어떤 쿼리를 통해 데이터가 페치되는지 찾을 필요 없이, 컴포넌트 자체에 선언된 fragment를 통해 확인 가능해진다.

### Fragment

Fragment는 GraphQL내에서 재사용 가능한 데이터 단위이다. 각 컴포넌트는 필요한 데이터를 Fragment를 통해 독립적으로 선언할 수 있다.

### Collocation

Collocation은 ‘나란히 배치하다’라는 의미를 가지고 있는데, GraphQL에서 Fragment를 컴포넌트 코드와 함께 배치함으로서 각 컴포넌트가 자신이 어떤 데이터에 의존성을 가지고 있는지를 명확히 설명하고 관리할 수 있도록 하는 패턴을 의미한다.

## Fragment를 사용하여 데이터 페칭 개선하기

Relay에서 useFragment 훅을 호출하여 Fragment를 정의할 수 있다.

```jsx
const data = useFragment(
  graphql`
    query Home_posts on Query {
      posts {
        ...BlogPost_post
      }
    }
  `,
  null
);
```

<aside>
💡 useFragment 훅은 두 개의 인자를 필요로한다.

1. 프래그먼트 리터럴(어떤 데이터 필드를 요청할지에 대한 요구사항)
2. 프래그먼트 참조(컴포넌트에 필요한 특정 데이터의 소스)
</aside>

위 코드에서는 `Home_posts`라는 이름으로 GraphQL 쿼리를 정의하고 있다. 코드를 자세히 풀어보자면 다음과 같다.

- `Home_posts` 쿼리는 `posts` 필드를 요청한다.
- `…BlogPost_post`, 즉 `posts`에 필요한 추가 데이터 필드를 요청한다.
  - 이는 `BlogPost` 컴포넌트가 사용할 `post` 객체의 구조를 정의한 프래그먼트를 참조한다.
- `useFragment`의 두 번째 인자로 `null`을 전달한다. 즉 여기서는 필요한 데이터의 로드를 트리거하지 않는다.
- 결과적으로 `data` 변수는 `posts` 필드에 대한 응답 데이터를 가지게 된다.
  - `data.posts`는 각 게시물에 대한 데이터 배열이다.
  - 이는 후속 렌더링에서 `BlogPost` 컴포넌트에 전달될 수 있다. (아래 코드에서 확인해보자!)

다시 전체 코드를 통해 로직이 어떻게 개선될 수 있는지 확인해보자.

```jsx
// pages/index.tsx
export default function Home() {
  const data = useFragment(
    graphql`
			query Home_posts on Query {
				posts {
					...BlogPost_post
				}
			}
		`,
    null
  );

  return (
    <div>
      {data.posts.map((post) => (
        <BlogPost key={post.id} post={post} />
      ))}
    </div>
  );
}
```

```jsx
// components/BlogPost.tsx
export default function BlogPost({ post }: { post: Post }) {
  const data = useFragment(
    graphql`
      fragment BlogPost_post on Post {
        title
        content
        comments {
          ...Comment_comment
        }
      }
    `,
    post
  );

  return (
    <div>
      <h1>{data.title}</h1>
      <p>{data.content}</p>
      <div>
        {data.comments.map((comment) => (
          <Comment key={comment.id} comment={comment} />
        ))}
      </div>
    </div>
  );
}
```

```jsx
// components/Comment.tsx
export default function Comment({ comment }: { comment: Comment }) {
  const data = useFragment(
    graphql`
      fragment Comment_comment on Comment {
        title
        content
      }
    `,
    comment
  );

  return (
    <div>
      <h2>{data.title}</h2>
      <p>{data.content}</p>
    </div>
  );
}
```

이제 `Comment` 컴포넌트는 데이터 페칭 로직으로부터 분리되었다. `Comment` 컴포넌트는 자신의 데이터 요구사항(`title`, `content`)를 자신의 Fragment에 정의했다. 만약 새로운 필드가 필요하다면 단순히 Fragment에 필드를 추가하면 자동으로 새 필드를 받게 된다. 또한 `Home` 컴포넌트는 `Comment` 컴포넌트가 필요로 하는 필드에 대해 더이상 신경쓸 필요가 없다.

각 컴포넌트가 필요로 하는 데이터를 Fragment를 통해서 표현하고, 이 컴포넌트에서 사용하는 상위 컴포넌트의 쿼리(혹은 또 다른 Fragment)에서 해당 컴포넌트를 위한 Fragment 데이터를 가져온 후 props로 전달했다. 이러한 방식을 통해, **데이터를 가져오는 코드와 가져온 데이터를 사용하는 코드가 여러 곳으로 분리**되는 문제를 해결한 것이다.

# 😷 Data Masking

---

Relay는 Data Masking을 제공하여 데이터를 캡슐화한다.

두 개의 형제 컴포넌트가 `comment` 데이터를 사용한다고 가정해보자. 두 컴포넌트 모두 자신의 별도 Fragment에서 자신이 필요한 데이터를 정의한다. 컴포넌트1은 `title` 필드만 필요로 하고, 컴포넌트2는 `author`와 `content` 필드를 필요로 한다.

이 때 두 컴포넌트에 직접 `comment` 데이터를 내려줄 경우, 컴포넌트2가 fragment에서 정의하지 않은 `title` 필드를 실수로 사용할 수 있다. 이는 두 컴포넌트 간의 의존성을 만들어 문제를 일으킬 수 있다.

이를 방지하기 위해, Relay는 컴포넌트에 데이터를 전달하기 전에 데이터를 마스킹하는 기능을 제공한다. **Fragment에서 특정 필드를 정의하지 않았다면, 이론적으로는 해당 필드가 데이터에 존재하더라도 그 필드에 대한 접근을 막는 것이다.** 오직, 자신이 요청한 데이터 필드에 대해서만 확인할 수 있다.

이러한 Relay의 작동은 필요하지 않은 데이터를 가져오는 일이 없도록 하여 오버 페칭 문제를 방지한다.

# 👀 참고자료

---

https://relay.dev/

https://wundergraph.com/blog/relay_wundergraph_integration_announcement

[https://velog.io/@xiniha/쉽게-배우는-Relay-1-Relay-소개](https://velog.io/@xiniha/%EC%89%BD%EA%B2%8C-%EB%B0%B0%EC%9A%B0%EB%8A%94-Relay-1-Relay-%EC%86%8C%EA%B0%9C)

https://velog.io/@juno7803/thinking-in-relay

### 딥다이브를 위해 읽어보면 좋을 글

https://emewjin.github.io/relay-style-graphql/

[https://devethan.medium.com/react-데이터-패치에-최적화된-relay-사용하기-7f8ec22bab72](https://devethan.medium.com/react-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%8C%A8%EC%B9%98%EC%97%90-%EC%B5%9C%EC%A0%81%ED%99%94%EB%90%9C-relay-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-7f8ec22bab72)
