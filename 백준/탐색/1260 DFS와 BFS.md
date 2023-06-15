# 1260 DFS와 BFS

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

let [N, M, V] = input[0].split(" ").map(Number);

let graph: boolean[][] = [];
let visited: boolean[] = new Array(N).fill(false);
visited[V - 1] = true;

let BFS: number[] = [V];
let DFS: number[] = [V];
let q: number[] = [V - 1];
let s: number[] = [V - 1];

for (let i = 0; i < N; i++) {
  let arr = new Array(N).fill(false);
  graph.push(arr);
}

for (let i = 0; i < M; i++) {
  let [A, B] = input[i + 1].split(" ").map(Number);

  graph[A - 1][B - 1] = true;
  graph[B - 1][A - 1] = true;
}

while (q.length !== 0) {
  let current = q.shift() as number;
  for (let i = 0; i < N; i++) {
    if (graph[current][i] && !visited[i]) {
      q.push(i);
      BFS.push(i + 1);
      visited[i] = true;
    }
  }
}

visited = new Array(N).fill(false);
visited[V - 1] = true;

while (s.length !== 0) {
  let current = s.pop() as number;

  if (!visited[current]) {
    DFS.push(current + 1);
    visited[current] = true;
  }

  for (let i = N - 1; i >= 0; i--) {
    if (graph[current][i] && !visited[i]) {
      s.push(i);
    }
  }
}

console.log(DFS.join(" "));
console.log(BFS.join(" "));
```
