# K번째 수

배열 array, [i, j, k]를 원소로 가진 2차원 배열 commands가 매개변수로 주어질 때, commands의 모든 원소에 대해 앞서 설명한 연산을 적용했을 때 나온 결과를 배열에 담아 return 하도록 solution 함수를 작성해주세요.

```javascript
function solution(array, commands) {
  var answer = [];

  for (let i = 0; i < commands.length; i++) {
    let subarr = array.slice(commands[i][0] - 1, commands[i][1]);
    answer.push(subarr.sort((a, b) => a - b)[commands[i][2] - 1]);
  }

  return answer;
}
```
