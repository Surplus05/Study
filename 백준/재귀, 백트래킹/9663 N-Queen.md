# 9663 N-Queen

2차원 배열로 생각해서 풀었다.  
queen 함수보다 queen 이 배치될 수 있는지 검사하는 함수가 더 힘들었다.  
정답은 맞게 나오나 시간초과가 발생했다.

```typescript
const path = require("path");
const filePath =
  process.platform === "linux"
    ? "/dev/stdin"
    : path.join(__dirname, "/input.txt");
const input = require("fs")
  .readFileSync(filePath)
  .toString()
  .trim()
  .split("\n")
  .map((s: string) => s.replace("\r", ""));

let N = parseInt(input[0]);

let board: number[][] = [];
let answer = 0;

for (let i = 0; i < N; i++) {
  let arr = new Array(N).fill(0);
  board.push(arr);
}

function placeable(row: number, col: number) {
  let placeable = true;
  // 가로세로 Check
  for (let i = 0; i < N; i++) {
    if (board[i][col] === 1 || board[row][i] === 1) {
      placeable = false;
      break;
    }
  }

  // 대각선 Check
  // 좌상
  let r = row - 1;
  let c = col - 1;
  while (r >= 0 && c >= 0) {
    if (board[r--][c--] === 1) {
      placeable = false;
      break;
    }
  }

  // 우하
  r = row + 1;
  c = col + 1;
  while (r < N && c < N) {
    if (board[r++][c++] === 1) {
      placeable = false;
      break;
    }
  }

  // 우상
  r = row - 1;
  c = col + 1;
  while (r >= 0 && c < N) {
    if (board[r--][c++] === 1) {
      placeable = false;
      break;
    }
  }

  // 좌하
  r = row + 1;
  c = col - 1;
  while (r < N && c > 0) {
    if (board[r++][c--] === 1) {
      placeable = false;
      break;
    }
  }
  return placeable;
}

function queen(idx: number, depth: number) {
  if (depth === N) {
    answer++;
    return;
  }

  for (let i = idx; i < N; i++) {
    for (let j = 0; j < N; j++) {
      if (!placeable(i, j)) continue;
      board[i][j] = 1;
      queen(i + 1, depth + 1);
      board[i][j] = 0;
    }
  }
}

queen(0, 0);

console.log(answer);
```

1차원 배열을 이용하라는 힌트를 얻었다.  
정리를 해 보자.  
[2,0,3,1] 의 의미는 2행 0열, 0행 1열, 3행 2열, 1행 3열에 퀸이 있음을 의미한다.

[[-Q--],  
[---Q],  
[Q---],  
[--Q-]]  
와 같은 의미.

배치될 수 있는지 여부는 어떻게 확인할까?

1. 같은 열 -> 한 열에는 하나의 index 정보만 올 수 있으므로 신경 쓸 필요 없다.
2. 같은 행 -> 1차원 배열을 Search해 같은 Number가 있는지 확인한다.
3. 대각선 -> 배열 Index 차이와 Number 차이를 비교하면 된다.  
   예를 들어 [1, 2, 0, 0] 같은 경우 1과 2를 비교하면 1의 차이가 난다.  
   이 둘의 배열 Index 차이도 1이니 대각선에 위치하는 것이다.  
   또 다른 예로 [1, 4, 3, 0] 을 생각 해 보자 1과 3은 배열 Index 차이가 2이며 Number 차이 또한 2이다. 그러므로 대각선에 위치한다.

구현하기 위해 세부적으로 들어가 보자.  
[3, 1, 0, 0] 인 경우 1과 0을 비교하면 대각선으로 판단할 수 있다.

배열을 전체 순회하는것이 아니라 그냥 배치하고, 해당 위치까지 배치가능한지 비교하자.  
즉 배치후 -> 배치가능 판별 -> 결과에 따라 계속 진행 or 백트래킹.

구현하며 추가 대각선의 경우 +와 -가 둘다 해당될 수 있다.  
[... 3, N, 5, ...] 좌 상단에 위치  
[... 7, N, 5, ...] 좌 하단에 위치
절댓값으로 비교하자.

구현 해 보았다.

```typescript
const path = require("path");
const filePath =
  process.platform === "linux"
    ? "/dev/stdin"
    : path.join(__dirname, "/input.txt");
const input = require("fs")
  .readFileSync(filePath)
  .toString()
  .trim()
  .split("\n")
  .map((s: string) => s.replace("\r", ""));

let N = parseInt(input[0]);

let board: number[] = new Array(N).fill(0);
let answer = 0;

function placeable(target: number) {
  for (let i = 0; i < target; i++) {
    if (
      board[target] === board[i] || // 1. 동일한 행에 배치된 게 있는지 검사.
      Math.abs(target - board[i]) === target - i // 2. 값과 배열 Index 차이 비교.
    )
      return false;
  }
  return true;
}

function queen(depth: number) {
  console.log(board);
  if (depth === N) {
    answer++;
    return;
  }

  for (let i = 0; i < N; i++) {
    console.log(depth, i);
    board[depth] = i; // 배치
    if (placeable(depth)) queen(depth + 1); // 배치한 위치까지 correct 한지 검사하고 correct 하면 진행
    board[depth] = 0; // 아니면 다음으로.
  }
}

queen(0);

console.log(answer);
```

## 문제점

1. 오타.  
   placeable 함수에서 board[target] 와 board[i] 의 차이를 비교해야 하는데, target 과 board[i]를 비교하고 있다.

2. 배열 초기값.  
   배열의 초기화를 0으로 시키고 있는데, 이 때문에 퀸이 0에 배치된 것으로 취급되어 제대로 작동하지 않았다. -> 라고 생각했지만, 비교를 depth까지만 하므로 상관 없다.

## 최종 코드

```typescript
const path = require("path");
const filePath =
  process.platform === "linux"
    ? "/dev/stdin"
    : path.join(__dirname, "/input.txt");
const input = require("fs")
  .readFileSync(filePath)
  .toString()
  .trim()
  .split("\n")
  .map((s: string) => s.replace("\r", ""));

let N = parseInt(input[0]);

let board: number[] = new Array(N).fill(-1);
let answer = 0;

function placeable(target: number) {
  for (let i = 0; i < target; i++) {
    if (
      board[target] === board[i] ||
      Math.abs(board[target] - board[i]) === target - i
    )
      return false;
  }
  return true;
}

function queen(depth: number) {
  if (depth === N) {
    answer++;
    return;
  }

  for (let i = 0; i < N; i++) {
    board[depth] = i;
    if (placeable(depth)) queen(depth + 1);
    board[depth] = -1;
  }
}

queen(0);

console.log(answer);
```

## 총평

2차원 배열 -> 1차원 배열 -> 문제점 수정

총 소요시간 약 2시간

2차원 배열 구현 (40분)  
1차원 배열 정리, 구현 (30분)  
문제점 발견하고 수정 -> (50분)

3년전 쯤 풀었던 문제였는데 되게 어렵게 느껴졌다.  
특히 사소한 것에서 시간을 진짜 많이 잡아먹었다.  
경험 쌓이다 보면 늘겠지?
