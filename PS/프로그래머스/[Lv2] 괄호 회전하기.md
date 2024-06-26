| 작성일     | 플랫폼       | 레벨 | 시도 | 키워드                        |
| ---------- | ------------ | ---- | ---- | ----------------------------- |
| 2024/05/21 | 프로그래머스 | Lv2  | 4    | `슬라이딩 윈도우` `원형 탐색` |

<details>
<summary style="font-size: 24px; font-weight:600">
문제
</summary>
<div markdown="1">

### **문제 설명**

다음 규칙을 지키는 문자열을 올바른 괄호 문자열이라고 정의합니다.

- `()`, `[]`, `{}` 는 모두 올바른 괄호 문자열입니다.
- 만약 `A`가 올바른 괄호 문자열이라면, `(A)`, `[A]`, `{A}` 도 올바른 괄호 문자열입니다. 예를 들어, `[]` 가 올바른 괄호 문자열이므로, `([])` 도 올바른 괄호 문자열입니다.
- 만약 `A`, `B`가 올바른 괄호 문자열이라면, `AB` 도 올바른 괄호 문자열입니다. 예를 들어, `{}` 와 `([])` 가 올바른 괄호 문자열이므로, `{}([])` 도 올바른 괄호 문자열입니다.

대괄호, 중괄호, 그리고 소괄호로 이루어진 문자열 `s`가 매개변수로 주어집니다. 이 `s`를 왼쪽으로 x (_0 ≤ x < (`s`의 길이)_) 칸만큼 회전시켰을 때 `s`가 올바른 괄호 문자열이 되게 하는 x의 개수를 return 하도록 solution 함수를 완성해주세요.

---

### 제한사항

- s의 길이는 1 이상 1,000 이하입니다.

---

### 입출력 예

| s        | result |
| -------- | ------ |
| "[](){}" | 3      |
| "}]()[{" | 2      |
| "[)(]"   | 0      |
| "}}}"    | 0      |

---

### 입출력 예 설명

**입출력 예 #1**

- 다음 표는 `"[](){}"` 를 회전시킨 모습을 나타낸 것입니다.

| x   | s를 왼쪽으로 x칸만큼 회전 | 올바른 괄호 문자열? |
| --- | ------------------------- | ------------------- |
| 0   | "[](){}"                  | O                   |
| 1   | "](){}["                  | X                   |
| 2   | "(){}[]"                  | O                   |
| 3   | "){}[]("                  | X                   |
| 4   | "{}[]()"                  | O                   |
| 5   | "}[](){"                  | X                   |

- 올바른 괄호 문자열이 되는 x가 3개이므로, 3을 return 해야 합니다.

**입출력 예 #2**

- 다음 표는 `"}]()[{"` 를 회전시킨 모습을 나타낸 것입니다.

| x   | s를 왼쪽으로 x칸만큼 회전 | 올바른 괄호 문자열? |
| --- | ------------------------- | ------------------- |
| 0   | "}]()[{"                  | X                   |
| 1   | "]()[{}"                  | X                   |
| 2   | "()[{}]"                  | O                   |
| 3   | ")[{}]("                  | X                   |
| 4   | "[{}]()"                  | O                   |
| 5   | "{}]()["                  | X                   |

- 올바른 괄호 문자열이 되는 x가 2개이므로, 2를 return 해야 합니다.

**입출력 예 #3**

- s를 어떻게 회전하더라도 올바른 괄호 문자열을 만들 수 없으므로, 0을 return 해야 합니다.

**입출력 예 #4**

- s를 어떻게 회전하더라도 올바른 괄호 문자열을 만들 수 없으므로, 0을 return 해야 합니다.

</div>
</details>

# 생각 과정

이전에 풀었던 원형 수열과 비슷한 방식으로(슬라이딩 윈도우) 탐색한다!

탐색 과정은 잘 설계했으나, ‘올바른 괄호 문자열’을 판단하는 예외처리를 잘못 설계했다.

### 처음 설계

1. (), [], {} 각각을 저장하는 stack을 3가지 만든다.
2. 여는 괄호가 나오면 queue에 push한다.
3. 닫는 괄호가 나오면
   1. queue가 비어있지 않다면 비운다.
   2. queue가 비어있다면 flag를 false로 바꾼다.

그러나 다음과 같은 반례를 고려해야했다.

- `[{]}` → 올바르지 않은 괄호

### 두번째 설계

1. queue가 아니라 후입선출인 stack을 사용해야 위와 같은 반례를 피할 수 있다.
2. stack을 3개로 나눌 필요 없이 하나로 합친다.
3. 닫는 괄호가 나왔을 때, 비어있는지 여부만 확인하는 것이 아니라 stack을 pop했을 때 나온 요소가 짝이 맞는 여는 괄호인지 확인하는 과정을 추가한다.

### 작성 코드

```jsx
function solution(s) {
  cnt = 0;
  for (let i = 0; i < s.length; i++) {
    // 슬라이딩 윈도우의 시작점
    flag = true;
    stack = [];
    for (let j = 0; j < s.length; j++) {
      // 윈도우 내에서 탐색
      if (s[(j + i) % s.length] == "[") {
        stack.push("[");
      } else if (s[(j + i) % s.length] == "(") {
        stack.push("(");
      } else if (s[(j + i) % s.length] == "{") {
        stack.push("{");
      }

      if (s[(j + i) % s.length] == "]") {
        if (stack.length == 0) {
          flag = false;
          break;
        } else {
          a = stack.pop();
          if (a !== "[") {
            flag = false;
            break;
          }
        }
      } else if (s[(j + i) % s.length] == ")") {
        if (stack.length == 0) {
          flag = false;
          break;
        } else {
          a = stack.pop();
          if (a !== "(") {
            flag = false;
            break;
          }
        }
      } else if (s[(j + i) % s.length] == "}") {
        if (stack.length == 0) {
          flag = false;
          break;
        } else {
          a = stack.pop();
          if (a !== "{") {
            flag = false;
            break;
          }
        }
      }
    }
    if (!stack.length == 0) {
      flag = false;
    }
    if (flag === true) {
      cnt++;
    }
  }
  return cnt;
}
```
