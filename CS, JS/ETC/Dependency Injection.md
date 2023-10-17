# 의존성 주입  

의존성 주입은 의존성을 직접 생성하거나 관리하지 않고 외부로부터 주입받는 것을 의미.  
유지보수성, 테스트 용이성, 재사용성 등 여러 장점을 갖는다.  

의존성 주입 전 코드를 살펴 보자.  
```ts
class CartService {
  private items: string[] = [];
  private capacity: number = 0;

  constructor(capacity: number) {
    this.capacity = capacity;
  }

  addItem(item: string) {
    if(item.length < this.capacity)
      this.items.push(item);
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

의존성을 한번 주입 해 보자.  
```ts
class CartService {
  private items: string[] = [];
  private capacity: number = 0;

  constructor(capacity: number) {
    this.capacity = capacity;
  }

  addItem(item: string) {
    if(item.length < this.capacity)
      this.items.push(item);
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

OrderService 는 의존성을 직접 생성하지 않고 외부에서 주입받는다.  
CartService 변경이나 다른 장바구니 서비스로 쉽게 교체가 가능해 진다.  

무조건적으로 좋은 것은 아닌데, 규모가 작은 프로젝트에서 의존성 주입을 과다하게 사용하는 경우 의존성 주입의 장점보다는 더 복잡해지는 결과를 낳는다.  
적절하게 사용하자 -> 경험이 중요한 이유.  

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
const serviceC = new ServiceC(serviceC);
const client = new Client(serviceA, serviceB, serviceC);
```

이렇게 복잡한 경우가 생길 수 있다.  
변경에 유연해지지 않아도 되는 것들이나 규모가 작은 것들은 꼭 의존성 주입 패턴으로 코드를 작성해야 하는지 사전에 잘 생각해보고 사용하자.
