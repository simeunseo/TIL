| 작성일       | 플랫폼       | 레벨 | 시도 | 키워드       |
| ------------ | ------------ | ---- | ---- | ------------ |
| May 23, 2024 | 프로그래머스 | Lv2  | 4    | `해시` `Map` |

<details>
<summary style="font-size: 24px; font-weight:600">
문제
</summary>
<div markdown="1">

### **문제 설명**

철수는 롤케이크를 두 조각으로 잘라서 동생과 한 조각씩 나눠 먹으려고 합니다. 이 롤케이크에는 여러가지 토핑들이 일렬로 올려져 있습니다. 철수와 동생은 롤케이크를 공평하게 나눠먹으려 하는데, 그들은 롤케이크의 크기보다 롤케이크 위에 올려진 토핑들의 종류에 더 관심이 많습니다. 그래서 잘린 조각들의 크기와 올려진 토핑의 개수에 상관없이 각 조각에 동일한 가짓수의 토핑이 올라가면 공평하게 롤케이크가 나누어진 것으로 생각합니다.

예를 들어, 롤케이크에 4가지 종류의 토핑이 올려져 있다고 합시다. 토핑들을 1, 2, 3, 4와 같이 번호로 표시했을 때, 케이크 위에 토핑들이 [1, 2, 1, 3, 1, 4, 1, 2] 순서로 올려져 있습니다. 만약 세 번째 토핑(1)과 네 번째 토핑(3) 사이를 자르면 롤케이크의 토핑은 [1, 2, 1], [3, 1, 4, 1, 2]로 나뉘게 됩니다. 철수가 [1, 2, 1]이 놓인 조각을, 동생이 [3, 1, 4, 1, 2]가 놓인 조각을 먹게 되면 철수는 두 가지 토핑(1, 2)을 맛볼 수 있지만, 동생은 네 가지 토핑(1, 2, 3, 4)을 맛볼 수 있으므로, 이는 공평하게 나누어진 것이 아닙니다. 만약 롤케이크의 네 번째 토핑(3)과 다섯 번째 토핑(1) 사이를 자르면 [1, 2, 1, 3], [1, 4, 1, 2]로 나뉘게 됩니다. 이 경우 철수는 세 가지 토핑(1, 2, 3)을, 동생도 세 가지 토핑(1, 2, 4)을 맛볼 수 있으므로, 이는 공평하게 나누어진 것입니다. 공평하게 롤케이크를 자르는 방법은 여러가지 일 수 있습니다. 위의 롤케이크를 [1, 2, 1, 3, 1], [4, 1, 2]으로 잘라도 공평하게 나뉩니다. 어떤 경우에는 롤케이크를 공평하게 나누지 못할 수도 있습니다.

롤케이크에 올려진 토핑들의 번호를 저장한 정수 배열 `topping`이 매개변수로 주어질 때, 롤케이크를 공평하게 자르는 방법의 수를 return 하도록 solution 함수를 완성해주세요.

---

### 제한사항

- 1 ≤ `topping`의 길이 ≤ 1,000,000
  - 1 ≤ `topping`의 원소 ≤ 10,000

---

### 입출력 예

| topping                  | result |
| ------------------------ | ------ |
| [1, 2, 1, 3, 1, 4, 1, 2] | 2      |
| [1, 2, 3, 1, 4]          | 0      |

---

### 입출력 예 설명

**입출력 예 #1**

- 롤케이크를 [1, 2, 1, 3], [1, 4, 1, 2] 또는 [1, 2, 1, 3, 1], [4, 1, 2]와 같이 자르면 철수와 동생은 각각 세 가지 토핑을 맛볼 수 있습니다. 이 경우 공평하게 롤케이크를 나누는 방법은 위의 두 가지만 존재합니다.

**입출력 예 #2**

- 롤케이크를 공평하게 나눌 수 없습니다.
</div>
</details>

# 생각 과정

### 시도1

완전탐색

1. for문으로 자르는 인덱스 결정
2. for문 안에서

   1. new Set(slice(0, i)).size
   2. new Set(slice(i, len-i)).size

   두 개가 같으면 cnt ++

```jsx
function solution(topping) {
  cnt = 0;
  for (let i = 1; i < topping.length; i++) {
    setA = new Set(topping.slice(0, i));
    setB = new Set(topping.slice(i, topping.length));

    if (setA.size === setB.size) {
      cnt++;
    }
  }
  return cnt;
}
```

→ 시간 초과

### 시도2

생각해보니 slice를 쓸 필요 없이, 완전탐색 하면서 자르는 인덱스의 앞인지 뒤인지만 확인하면 된다.

```jsx
function solution(topping) {
  cnt = 0;
  setA = new Set();
  setB = new Set();
  for (let i = 1; i < topping.length; i++) {
    for (let j = 0; j < topping.length; j++) {
      if (j < i) {
        setA.add(topping[j]);
      } else {
        setB.add(topping[j]);
      }
    }

    if (setA.size === setB.size) {
      cnt++;
    }
  }
  return cnt;
}
```

→ 역시나 시간초과에, 반례까지 생겼다

### 시도3

전부 탐색할 필요 없는 방법을 찾아야 한다.

1. topping을 pop하면서 새로운 배열에 push한다.
2. Set(topping).size === Set(arr).size이면 cnt++
3. cnt가 0이 아니면서 Set(arr).size가 더 커지는 순간 break
4. 예외처리 : topping.length === 0이면 break

```jsx
function solution(topping) {
  cnt = 0;
  setA = new Set();
  setB = new Set();
  arr = [];
  while (true) {
    arr.push(topping.pop());
    if (topping.length === 0) {
      break;
    }
    if (new Set(topping).size === new Set(arr).size) {
      cnt++;
    }
    if (cnt != 0 && new Set(arr).size > new Set(topping).size) {
      break;
    }
  }
  return cnt;
}
```

→ 여전히 시간 초과

계속해서 Set을 초기화하는 과정에서 시간이 낭비되는 것 같다.

### 시도4

객체를 사용하여 각 토핑의 재료가 몇개인지 저장하는 방법을 시도했다. 그리고 topping을 순회하면서 객체에서 각 재료의 수를 줄이고 새로운 Set에는 추가해준다.

```jsx
function solution(topping) {
  cnt = 0;
  a = {};
  b = new Set();
  for (let i of topping) {
    a[i] = (a[i] || 0) + 1;
  }

  for (let i = 0; i < topping.length; i++) {
    if (a[topping[i]] === 1) {
      delete a[topping[i]];
    } else {
      a[topping[i]]--;
    }
    b.add(topping[i]);

    if (Object.keys(a).length === b.size) {
      cnt++;
    }
  }

  return cnt;
}
```

→ 통과하는 케이스가 늘었지만, 여전히 시간초과

→ b도 객체로 바꿔보았지만 결과는 동일하다.

→ `Object.keys(a).length`에서 시간이 많이 걸리는 듯하다.

### 시도5

자료구조를 객체나 Set이 아닌 Map을 사용해본다.

```jsx
function solution(topping) {
  cnt = 0;
  a = new Map();
  b = new Map();
  for (let i of topping) {
    if (a.has(i)) {
      a.set(i, a.get(i) + 1);
    } else {
      a.set(i, 1);
    }
  }

  for (let i = 0; i < topping.length; i++) {
    if (a.get(topping[i]) === 1) {
      a.delete(topping[i]);
    } else {
      a.set(topping[i], a.get(topping[i]) - 1);
    }

    if (b.has(topping[i])) {
      b.set(topping[i], b.get(topping[i]) + 1);
    } else {
      b.set(topping[i], 1);
    }

    if (a.size === b.size) {
      cnt++;
    }
  }

  return cnt;
}
```

**→ 통과!**

> Map 자료형이 익숙하지 않아서 피하려 했는데, 시간복잡도가 중요할 때 잘 활용해야겠다.
