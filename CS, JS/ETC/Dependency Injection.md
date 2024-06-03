# 의존성 주입
의존성 주입은 직접 생성하거나 관리하지 않고 외부로부터 주입받는 방법을 의미한다.  

의존성 주입 전 코드

```ts
class CartService {
  private items: string[] = [];
  private capacity: number = 0;

  constructor(capacity: number) {
    this.capacity = capacity;
  }

  addItem(item: string) {
    if(item.length < this.capacity) {
      this.items.push(item);
    }
  }

  getItems() {
    return this.items;
  }
}

class OrderService {
  private cartService: CartService;

  constructor() {
    this.cartService = new CartService(10); // 의존성을 직접 생성
  }

  placeOrder() {
    const items = this.cartService.getItems();
    // 주문을 처리하는 로직
  }
}

const orderService = new OrderService();
orderService.placeOrder();
```

위 코드에서 OrderService는 CartService를 직접 생성하고 있는데, 이는 CartService의 변경이 필요할 때마다 OrderService를 수정해야 한다는 단점을 갖고 있다.  

의존성을 주입 해 보자.  

```ts
class CartService {
  private items: string[] = [];
  private capacity: number = 0;

  constructor(capacity: number) {
    this.capacity = capacity;
  }

  addItem(item: string) {
    if(item.length < this.capacity) {
      this.items.push(item);
    }
  }

  getItems() {
    return this.items;
  }
}

class OrderService {
  private cartService: CartService;

  constructor(cartService: CartService) {
    this.cartService = cartService; // 의존성 주입
  }

  placeOrder() {
    const items = this.cartService.getItems();
    // 주문을 처리하는 로직
  }
}

const cartService = new CartService(15);
const orderService = new OrderService(cartService); // 의존성 주입

orderService.placeOrder();
```
위 코드에서 OrderService는 CartService를 외부에서 주입받는데, 이렇게 하면 CartService의 변경이나 다른 장바구니 서비스로의 교체가 용이해진다.  

```ts
class ServiceA {
  // ...
}

class ServiceB {
  // ...
}

class ServiceC {
  // ...
}

class Client {
  private serviceA: ServiceA;
  private serviceB: ServiceB;
  private serviceC: ServiceC;

  constructor(serviceA: ServiceA, serviceB: ServiceB, serviceC: ServiceC) {
    this.serviceA = serviceA; // 의존성 주입
    this.serviceB = serviceB; // 의존성 주입
    this.serviceC = serviceC; // 의존성 주입
  }

  // ...
}

const serviceA = new ServiceA();
const serviceB = new ServiceB(serviceA);
const serviceC = new ServiceC(serviceB);
const client = new Client(serviceA, serviceB, serviceC);
```
의존성 주입을 남발하면 관리가 어려워질 수 있다.  
모든 의존성을 주입받아야 하는 상황이 오히려 코드의 복잡도를 높이고, 개발 생산성을 저해할 수 있다.

변경에 유연해지지 않아도 되는 것들이나 규모가 작은 것들은 꼭 의존성 주입 패턴으로 코드를 작성해야 하는지 사전에 잘 생각해보고 사용하자.
