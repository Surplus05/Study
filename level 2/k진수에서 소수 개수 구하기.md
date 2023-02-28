# k진수에서 소수 개수 구하기

정수 n과 k가 매개변수로 주어집니다. n을 k진수로 바꿨을 때, 변환된 수 안에서 찾을 수 있는 위 조건에 맞는 소수의 개수를 return 하도록 solution 함수를 완성해 주세요.

```javascript
function solution(n, k) {
  var answer = 0;
  let arr = n.toString(k).split("0");
  arr = arr.map((n) => parseInt(n)).filter((n) => n);
  for (let i = 0; i < arr.length; i++) {
    let sqrt = Math.sqrt(arr[i]);
    let isPrime = true;
    if (arr[i] === 1) continue;
    for (let j = 2; j <= sqrt; j++) {
      if (arr[i] % j === 0) {
        isPrime = false;
        break;
      }
    }
    if (isPrime) answer++;
  }

  return answer;
}
```
