# JadenCase 문자열 만들기

문자열 s가 주어졌을 때, s를 JadenCase로 바꾼 문자열을 리턴하는 함수, solution을 완성해주세요.

```javascript
function solution(s) {
  var answer = "";
  let words = s.split(" ");

  words = words.map((word) => {
    if (!isNaN(word[0]) || word === "") {
      return word.toLowerCase();
    } else {
      return (
        word[0].toUpperCase() + word.substring(1, word.length).toLowerCase()
      );
    }
  });

  return words.join(" ");
}
```
