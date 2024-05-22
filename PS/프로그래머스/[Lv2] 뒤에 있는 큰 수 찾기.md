| 작성일       | 플랫폼       | 레벨 | 시도 | 키워드 |
| ------------ | ------------ | ---- | ---- | ------ |
| May 23, 2024 | 프로그래머스 | Lv2  | 2    | `스택` |

<details>
<summary style="font-size: 24px; font-weight:600">
문제
</summary>
<div markdown="1">

### **문제 설명**

정수로 이루어진 배열 `numbers`가 있습니다. 배열 의 각 원소들에 대해 자신보다 뒤에 있는 숫자 중에서 자신보다 크면서 가장 가까이 있는 수를 뒷 큰수라고 합니다.

정수 배열 `numbers`가 매개변수로 주어질 때, 모든 원소에 대한 뒷 큰수들을 차례로 담은 배열을 return 하도록 solution 함수를 완성해주세요. 단, 뒷 큰수가 존재하지 않는 원소는 -1을 담습니다.

---

### 제한사항

- 4 ≤ `numbers`의 길이 ≤ 1,000,000
  - 1 ≤ `numbers[i]` ≤ 1,000,000

---

### 입출력 예

| numbers            | result                |
| ------------------ | --------------------- |
| [2, 3, 3, 5]       | [3, 5, 5, -1]         |
| [9, 1, 5, 3, 6, 2] | [-1, 5, 6, 6, -1, -1] |

---

### 입출력 예 설명

입출력 예 #1

2의 뒷 큰수는 3입니다. 첫 번째 3의 뒷 큰수는 5입니다. 두 번째 3 또한 마찬가지입니다. 5는 뒷 큰수가 없으므로 -1입니다. 위 수들을 차례대로 배열에 담으면 [3, 5, 5, -1]이 됩니다.

입출력 예 #2

9는 뒷 큰수가 없으므로 -1입니다. 1의 뒷 큰수는 5이며, 5와 3의 뒷 큰수는 6입니다. 6과 2는 뒷 큰수가 없으므로 -1입니다. 위 수들을 차례대로 배열에 담으면 [-1, 5, 6, 6, -1, -1]이 됩니다.

</div>
</details>

# 생각 과정

### 시도1

2중 for문 완전 탐색부터 시도했다.

1. for i=0…numbers.length-1
2. for j=i+1…numbers.length-1
3. numbers[i] < numbers[j] 이면 break하고 res에 push(j)

```jsx
function solution(numbers) {
  const res = [];
  for (let i = 0; i < numbers.length; i++) {
    for (let j = i + 1; j < numbers.length; j++) {
      if (numbers[i] < numbers[j]) {
        res.push(numbers[j]);
        break;
      }
      if (j === numbers.length - 1) {
        res.push(-1);
      }
    }
    if (i === numbers.length - 1) {
      res.push(-1);
    }
  }
  return res;
}
```

→ 시간초과

### 시도2

여러 자료구조를 고려해보다가, stack을 사용한 정답을 참고하게 됐다.

```jsx
function solution(numbers) {
  const ans = new Array(numbers.length).fill(-1);
  const stack = [];

  for (let i = 0; i < numbers.length; i++) {
    while (numbers[stack.at(-1)] < numbers[i]) {
      const cur = stack.pop();
      ans[cur] = numbers[i];
    }
    stack.push(i);
  }
  return ans;
}
```

다음과 같은 로직이다.

1. answer 배열을 -1로 초기화해둔다.
2. numbers 배열을 i…numbers.length로 순회하면서,
   1. stack에 i를 넣는다
   2. stack의 마지막 값보다 numbers[i]가 더 크다면

      → 뒷 큰수를 찾은 것

      → 현재 stack에 있는 모든 값의 뒷 큰수는 numbers[i]이다

   3. stack에서 값을 모두 빼면서 그 인덱스의 뒷큰수는 numbers[i]임을 answer에 저장한다.

stack을 사용할 생각은 했지만 stack에 값이 아닌 인덱스를 넣을 생각을 하지 못했다. 또한 뒷 큰수가 나오지 않는 구간 동안에 거쳐간 값들의 뒷 큰수는 모두 같다는 사실을 캐치하지 못했다.

> stack 등을 사용할 때 어떤 데이터를 넣어서 어떻게 활용할건지에 대해 다방면으로 생각해보는 것이 좋겠다.
