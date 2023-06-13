# N으로 표현

25 를 5로 표현한다고 하자.

() 안의 5가 아닌 숫자는 횟수를 의미.

25 = (24) + (5 / 5)  
25 = (26) - (5 / 5)  
25 = (125) / 5  
25 = (5) \* 5

4가지 CASE 존재.

5로 구성된 수는 최적해가 정해 져 있음.

5, 55, 555 ...

최초 방문이 최적해라 하고, 전파해 나가자.

12 = (55 + 5) / 5 가 최적인걸 보아 dp 배열의 길이를 가변으로 잡기가 애매하다.

32,000 이 조건이니 그냥 32,000까지 전부 구하자.

```javascript
function solution(N, number) {
  let answer = 0;

  let init = N;
  let val = 0;

  let dp = new Array(32001).fill(-1);

  let q = [];
  let front = [];

  while (init <= 32000) {
    dp[init] = val + 1;
    q.push(init);
    init += 10 ** ++val * N;
  }

  while (front != q.length) {
    let value = q[front++];

    if (value + N <= 32000 && dp[value + N] == -1) {
      dp[value + N] = dp[value] + 1;
      q.push(value + N);
    }

    if (value - N >= 0 && dp[value - N] == -1) {
      dp[value - N] = dp[value] + 1;
      q.push(value - N);
    }

    if (value * N <= 32000 && dp[value * N] == -1) {
      dp[value * N] = dp[value] + 1;
      q.push(value * N);
    }

    if (value % N === 0 && dp[value / N] == -1) {
      dp[value / N] = dp[value] + 1;
      q.push(value / N);
    }
  }
  return dp[number] > 8 ? -1 : dp[number];
}
```

오류 발생.

2, 2, 3, 4, 4, 1, 3, 4, 5, 5, 2, 3 -> 5에 대한 dp배열.

10이 2 (5 \* 5).

9가 5가 나오고 있음. 최적값은 (10) - (5/5) 로 4가 되어야 함.

최초방문이 최적해가 아닐 수 있으므로 조건을 변경 해 주자.

그리고 if문에 빠진게 존재한다.

N/N 을 추가하거나 제거해서 최적해를 구하는 경우가 빠져있다. 추가해 주자.

N을 사칙연산 -> 1 추가.

1을 사칙연산 -> 2 추가. 단, 나눗셈 곱셈은 의미 없으므로 제외.

```javascript
function solution(N, number) {
  let answer = 0;

  let init = N;
  let val = 0;

  let dp = new Array(32001).fill(-1);

  let q = [];
  let front = [];

  while (init <= 32000) {
    dp[init] = val + 1;
    q.push(init);
    init += 10 ** ++val * N;

    if (init == number) return val + 1;
  }

  while (front != q.length) {
    let value = q[front++];

    if (
      value + N <= 32000 &&
      (dp[value + N] == -1 || dp[value + N] > dp[value] + 1)
    ) {
      dp[value + N] = dp[value] + 1;
      q.push(value + N);
    }

    if (
      value - N >= 0 &&
      (dp[value - N] == -1 || dp[value - N] > dp[value] + 1)
    ) {
      dp[value - N] = dp[value] + 1;
      q.push(value - N);
    }

    if (
      value * N <= 32000 &&
      (dp[value * N] == -1 || dp[value * N] > dp[value] + 1)
    ) {
      dp[value * N] = dp[value] + 1;
      q.push(value * N);
    }

    if (
      value % N === 0 &&
      (dp[value / N] == -1 || dp[value / N] > dp[value] + 1)
    ) {
      dp[value / N] = dp[value] + 1;
      q.push(value / N);
    }

    if (
      value + 1 <= 32000 &&
      (dp[value + 1] == -1 || dp[value + 1] > dp[value] + 2)
    ) {
      dp[value + 1] = dp[value] + 2;
      q.push(value + 1);
    }

    if (
      value - 1 >= 0 &&
      (dp[value - 1] == -1 || dp[value - 1] > dp[value] + 2)
    ) {
      dp[value - 1] = dp[value] + 2;
      q.push(value - N);
    }
  }
  return dp[number] > 8 ? -1 : dp[number];
}
```

5, 6, 7, 8 케이스에서 오류가 발생..
