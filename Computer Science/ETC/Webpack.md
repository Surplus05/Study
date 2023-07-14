# Webpack

NextJS 에서는 TurboPack (Rust-based successor to Webpack)라는것을 사용하고 있다.  
Webpack 에 대해 알아보도록 하자.

Webpack은 Bundler다.

bundle? 묶다라는 의미.

무엇을 ? HTML, CSS, JS, Image 등 자원(asset)들을 묶어주는(번들링) 도구이다.  
종속성이 존재하는 파일들을 하나의 파일로 묶어 패키징을 시키는 것.

## Without Webpack

- a.js

```javascript
var word = "a";
```

- b.js

```javascript
var word = "b";
```

```html
<html>
  <head>
    <script src="./a.js"></script>
    <script src="./b.js"></script>
  </head>
  <body>
    <div id="root"></div>
    <script>
      document.querySelector("#root").innerHTML = word;
    </script>
  </body>
</html>
```

a를 출력하고 싶었으나 의도대로 동작하지 않았다.

## Module

- a.js

```javascript
var word = "a";
export default word;
```

- b.js

```javascript
var word = "b";
export default word;
```

```html
<html>
  <head> </head>
  <body>
    <div id="root"></div>
    <script type="module">
      import a_word from "./a.js";
      import b_word from "./b.js";
      document.querySelector("#root").innerHTML = a_word;
    </script>
  </body>
</html>
```

이제 제대로 a를 출력한다.

그러나 import 할 것들이 매우 많아진다면 ? ...

그러면 필요한 것들을 한번에 묶어서 다운로드받으면 어떨까 ?

한번에 묶어주기 위한것이 Bundler, 그 중 가장 인기 있는 구체적인 도구가 Webpack인 것이다.

위 코드에서 WebPack을 도입 해 보자.

Webpack 을 설치해 주고 어떻게 사용하는지 살펴 보자.

```
npm install -D webpack webpack-cli
```

이 부분을 따로 빼내자.

```html
<script type="module">
  import a_word from "./a.js";
  import b_word from "./b.js";
  document.querySelector("#root").innerHTML = a_word;
</script>
```

- index.js

```javascript
import a_word from "./a.js";
import b_word from "./b.js";
document.querySelector("#root").innerHTML = a_word;
```

index.js 는 a.js와 b.js를 사용하고 있음.

```
npx webpack --entry ./index.js --output ./public/index_bundle.js
```

index.js 파일과 그 파일이 사용하는 모든 파일들을 하나로 만들어서 index_bundle.js를 생성.

```html
<html>
  <body>
    <div id="root"></div>
    <script src="./public/index_bundle.js"></script>
  </body>
</html>
```

이렇게 작성하면 똑같이 동작한다.

100개, 1000개, ... n개의 모듈을 하나로 다운로드 받는다.

일반적으로 용량이 늘어나는것 보다 연결이 늘어나는것이 더 OverHead가 크므로 훨씬 효율적일 것이다.

만약 index.js 파일 하나 뿐이 아니라면 명령어 하나하나 다 쳐주어야 하는가? 아니다.  
config파일을 통해 자동화할 수 있다.

```javascript
const path = require("path");

module.export = {
  entry: "./index.js",
  output: {
    // __dirname 는 현재경로
    // 뒤 인자로 추가경로 전달
    path: path.resolve(__dirname, "public"),
    filename: "index_bundle.js",
  },
};
```

두 개는 동일하다.

```
npx webpack --entry ./index.js --output ./public/index_bundle.js

npx webpack --config webpack.config.js
```

다른 속성들은 https://webpack.kr/concepts/configuration/
에서 살펴볼 수 있다.

## Summary

Webpack을 왜 쓰는지, 어떻게 쓰는지, 어떻게 잘 쓰는지 알아 보았다.

Create React App, Create Next App 을 통해 매번 자동으로 설정해 줘서 제대로 알지 못했는데, 알 수있게 되었다.
