# 27969 I LOVE JavaScript

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

let string = input[0].split(" ");

let stack: number[] = [0];
let t = 0;

for (let i = 0; i < string.length; i++) {
  if (string[i] === "[") {
    stack[++t] = 0;
  } else if (string[i] == "]") {
    stack[--t] += stack[t + 1] + 8;
  } else {
    if (!isNaN(string[i])) {
      stack[t] += 8;
    } else {
      stack[t] += string[i].length + 12;
    }
  }
}

console.log(stack[0]);
```
