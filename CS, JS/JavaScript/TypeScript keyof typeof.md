# keyof 와 typeof

## keyof

객체의 key만 추출한다고 생각하자.  
객체 타입의 프로퍼티 key를 문자열 리터럴 유니온 형태로 추출한다.

![예제](TypeScript%20keyof%20typeof.png)

```ts
//Countries 변수의 type은 다음과 같다.

"Korea" | "Japan" | "China";

// 변수말고 타입으로도 가능하다.

type Cities = keyof Capital;
```

## typeof

변수나 객체의 타입을 추론한다.

```ts
type Capital = typeof CAPITAL;
// { Korea: string; Japan: number; China: string;}
```

## keyof typeof

변수 타입 프로퍼티의 key만 추출한다.

![예제2](TypeScript%20keyof%20typeof2.png)
