# this

this는 함수 호출 방식에 따라 달라져 다른 프로그래밍 언어와 차이가 존재한다.

## 일반 함수에서의 this

```typescript
function f() {
  console.log(this);
}
```

전역 객체를 참조

## 객체 메서드의 this

```typescript
let userA = { name: "A" };
let userB = { name: "B" };

function f() {
  console.log(this);
}

// 별개의 객체에서 동일한 함수를 사용함
userA.f = f;
userB.f = f;

userA.f();
userB.f();
```

호출 결과는 다음과 같음.

```
{ name: 'A', f: [Function: f] }
{ name: 'B', f: [Function: f] }
```

호출한 객체, Caller를 참조

## 생성자 함수에서의 this

```typescript
function UserInfo(name, age) {
  this.name = name;
  this.age = age;
}

let user = new UserInfo("John", 30);

console.log(user);
```

생성되는 객체를 참조

## 화살표 함수

화살표 함수에서는 자체 this값을 갖지 않고 외부에서 this 값을 가져오게 됨.

```typescript
let user = {
  firstName: "보라",
  sayHi() {
    let arrow = () => alert(this.firstName);
    arrow(); // Caller가 없음, 외부의 this값 참조
  },
};

user.sayHi(); // 보라
```
