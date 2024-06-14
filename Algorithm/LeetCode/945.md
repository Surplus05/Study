1. 정렬
2. 정렬된 배열 순회하며 이전 요소보드 작거나 같은지 검사
3. 작거나 같다면 현재 요소에 이전 요소와의 차이+1만큼 더함.
4. 이 더한 값이 Increment가 됨.

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
const minIncrementForUnique = function (nums) {
  let sortedArray = nums.sort((a, b) => {
    return a - b;
  });

  let answer = 0;

  for (let i = 1; i < sortedArray.length; i++) {
    if (sortedArray[i - 1] >= sortedArray[i]) {
      let diff = sortedArray[i - 1] - sortedArray[i] + 1;

      sortedArray[i] += diff;
      answer = answer + diff;
    }
  }

  return answer
};
```
