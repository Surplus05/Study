# 14567 선수과목 (Prerequisite)

위상정렬 변형문제.

0인것들을 하나하나 찾아서 구해준게 기존 위상정렬이라면 이 문제는 0인 것들을 모두 찾아 동일 단계(학기) 취급.

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

// let [N, M] = input[0].split(" ").map(Number);
// let N = Number(input[0]);

let [N, M] = input[0].split(" ").map(Number);

let condition = input.slice(1).map((s: string) => s.split(" ").map(Number));
let list: number[][] = [];
let Prerequisites: number[] = new Array(N + 1).fill(0);

let answer: number[] = new Array(N + 1).fill(0);

for (let i = 0; i <= N; i++) {
  list.push([]);
}

for (let i = 0; i < M; i++) {
  let [pre, target] = condition[i];
  list[pre].push(target);
}

for (let i = 0; i <= N; i++) {
  for (let j = 0; j < list[i].length; j++) {
    Prerequisites[list[i][j]]++;
  }
}

Prerequisites[0] = -1;

let semester = 1;

while (true) {
  let zeros = Prerequisites.map((value: number, index: number) => [
    value,
    index,
  ])
    .filter((value: number[]) => {
      return value[0] == 0;
    })
    .map((value: number[]) => value[1]);

  if (zeros.length == 0) break;

  for (let i = 0; i < zeros.length; i++) {
    Prerequisites[zeros[i]] = -1;
    answer[zeros[i]] = semester;
  }

  for (let i = 0; i < zeros.length; i++) {
    for (let j = 0; j < list[zeros[i]].length; j++) {
      Prerequisites[list[zeros[i]][j]]--;
    }
  }

  semester++;
}

console.log(answer.slice(1).join(" "));
```
