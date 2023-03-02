# 2 x n 타일링

보자마자 DP 냄새가 확 났다.  
N 을 구하기 위해서는 N-1, N-2 에서 결과에 계산을 하면 되는것까진 감이 왔으나 어떻게 계산해야 하는지는 생각이 떠오르지 않았다.  
경우의수를 따져 보며, 중복을 어떻게 COUNT 하는지 고민하다가 직접 그려보니 피보나치 수열이라 설마 싶었는데 진짜였다.  
흠..

```javascript
function solution(n) {
  let dp = new Array(n);
  dp[0] = 1;
  dp[1] = 2;

  for (let i = 2; i < n; i++) {
    dp[i] = (dp[i - 1] + dp[i - 2]) % 1000000007;
  }

  return dp[n - 1];
}
```
