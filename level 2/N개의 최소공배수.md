# N개의 최소공배수

n개의 숫자를 담은 배열 arr이 입력되었을 때 이 수들의 최소공배수를 반환하는 함수, solution을 완성해 주세요.

```javascript
function gcd(a, b) {
  if (a % b === 0) return b;
  return gcd(b, a % b);
}

function solution(arr) {
  var answer = 0;
  while (arr.length !== 1) {
    let a = arr.pop();
    let b = arr.pop();
    let lcm = (a * b) / gcd(a, b);
    arr.push(lcm);
  }
  return arr[0];
}
```
