# 14754 Pizza Boxes

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

let [N, M] = input[0].split(" ").map(Number);

let boxes = input.slice(1).map((box: string) => box.split(" ").map(Number));

let removed: number[][] = [];

let ans = 0;

for (let i = 0; i < N; i++) {
  let arr = new Array(M).fill(0);
  removed.push(arr);
}

// side view
for (let i = 0; i < N; i++) {
  let max = 0;

  for (let j = 0; j < M; j++) {
    if (boxes[i][max] < boxes[i][j]) {
      max = j;
    }
  }

  removed[i][max] = boxes[i][max];
}

// front view
for (let i = 0; i < M; i++) {
  let max = 0;

  for (let j = 0; j < N; j++) {
    if (boxes[max][i] < boxes[j][i]) {
      max = j;
    }
  }

  removed[max][i] = boxes[max][i];
}

for (let i = 0; i < N; i++) {
  for (let j = 0; j < M; j++) {
    if (removed[i][j] === 0) ans += boxes[i][j];
  }
}

console.log(ans);
```
