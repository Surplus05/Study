# 12018 Yonsei TOTO

신청현황 정렬 -> 수강인원 Index 의 마일리지 값이 필요한 마일리지 최소값이 됨.

이걸 따로 배열에 저장하고, 이 배열을 정렬해서 작은것 부터 담으면 최대 수강가능한 강의수를 구할 수 있다.

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

let required: number[] = [];
let used = 0;
let classes = 0;

for (let i = 0; i < N; i++) {
  let [P, L] = input[i * 2 + 1].split(" ").map(Number);

  let list = input[i * 2 + 2]
    .split(" ")
    .map(Number)
    .sort((a: number, b: number) => b - a);

  if (list[L - 1] == null) {
    required.push(1);
  } else {
    required.push(list[L - 1]);
  }
}

required.sort((a: number, b: number) => a - b);

for (let i = 0; i < required.length; i++) {
  if (required[i] + used <= M) {
    used += required[i];
    classes++;
  } else {
    break;
  }
}

console.log(classes);
```
