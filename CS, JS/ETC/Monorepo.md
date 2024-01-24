# Monorepo  

담당하는 서비스가 크게 두 도메인으로 나누어지고 iframe과 postMessage를 통해 정보를 주고 받으므로, 일종의 server와 client 구조로 볼 수 있다.  
이에 공통적인 요소들을 묶고, 의존성관리를 편하게 하기 위해 Monorepo를 도입하기로 했다.  
CI/CD 파이프라인에서 빌드가 별도로 구성되어 있었으므로 중복 패키지 설치와 시간 낭비가 발생했었기도 했어서 시간이 오래 걸리고 불편했다.  

조사를 해보니 주로 lerna, yarn berry 등이 있었다.

lerna의 경우 러닝커브가 비교적 높다고 하고, 더이상 유지보수가 이루어지지 않는다 하여 제외했다.  
yarn berry의 경우 별도의 설치가 필요 없다는 점도 매력적이었다.  
Zero-Install, Phantom Dependency, 느린 I/O호출 개선 등의 장점이 있는데 다른 좋은 블로그들을 참고하자.  

## 시작하기

``` bash
yarn init -2
```

* root package.json 설정하기  
  workspace 에 모노레포 명을 추가 해 주자.
  
  모노레포 폴더를 설정 해 주고, 각 모노레포를 yarn create vite 를 통해 프로젝트 생성해 주었다.

## 공통 설정하기  
tsconfig, eslint, prettier 등 설정을 공유할수 있는것이 모노레포 구조의 장점 중 하나이다.  

``` bash
yarn add -D typescript prettier eslint
yarn dlx @yarnpkg/sdks vscode
```

root 에서 공통 설정을 위한 설정을 해 준다.  


* tsconfig  
  모노레포에서는 root의 config를 활용한다.  
  모노레포의 tsconfig에서도 compilerOptions 속성을 추가해 주면 모노레포별 설정을 별도로 설정해줄 수 있다.  
  
  ```json
  {
    "extends": "../../tsconfig.common.json",
    "include": ["src"],
    "references": [{ "path": "./tsconfig.node.json" }],
  }
  ```

* eslint, prettier  
  root에서 관리한다.

  ```js
  module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react-hooks/recommended",
    "prettier",
  ],
  ignorePatterns: ["dist", ".eslintrc.cjs"],
  parser: "@typescript-eslint/parser",
  plugins: ["react-refresh"],
  rules: {
    "react-refresh/only-export-components": [
      "warn",
      { allowConstantExport: true },
    ],
  },
  };
  ```

  ```json
  {
  "singleQuote": false,
  "semi": true,
  "tabWidth": 2,
  "trailingComma": "all",
  "printWidth": 80,
  "arrowParens": "always"
  }
  ```

  둘 다 root에 위치시키면 된다.

  
* 패키지 설치
  root에 설치시 전 레포에서 사용 가능하다.
  만약 해당 레포만 사용하려면 해당 레포에서만 설치해주면 된다.
  
* 모노레포 공유 (공통사항 가져오기)
  common 이라는 폴더를 만들고, yarn init으로 초기 설정을 해 준다.
  공유하길 원하는 것들을 export 해주고, 사용할 모노레포에서 dependencies 에 common의 name을 추가해주면 사용이 가능하다.  

세세하게 해준게 더 많지만, 굵직하게 이정도만 해주면 쉽게 구축이 완료된다.

* 참고  
  https://d2.naver.com/helloworld/7553804  
  https://techblog.woowahan.com/7976/  
  https://toss.tech/article/node-modules-and-yarn-berry  
  https://www.testbank.ai/42b54c4b-2aa7-4bc7-b29b-b7219c700f22
  https://kasterra.github.io/setting-yarn-berry/

  


