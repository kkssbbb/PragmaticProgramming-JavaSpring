# 04장: SOLID 원칙

## 04장 소개

SOLID는 로버트 C. 마틴이 객체지향에서 좋은 설계를 얻기위해 개발자가 지겨야할 규범을 고안 해내어, 각 원칙의 앞 글자를 따고 각 원칙을 통칭해서 SOLID원칙 이라고 부른다.
그리고 각 원칙의 목표는 소프트웨어의 유지보수성과 확정성을 높이는 것이다.

>"SOLID 라는 원칙이 생기고 지금까지 중요하게 여겨지는걸 보면, 객체지향의 본질은 소프트웨어의 유지보수성과 확장성을 높이는게 본질이 아닐까 생각한다." -ksb

### SOLID 원칙 요약
| 원칙 | 이름 | 핵심 내용 | 주요 목적 |
|------|------|-----------|----------|
| **S** | Single Responsibility Principle | 클래스를 변경해야 할 이유는 단 하나여야 한다 | 변경으로 인한 영향 범위 최소화 |
| **O** | Open/Closed Principle | 클래스의 동작을 수정하지 않고 확장할 수 있어야 한다 | 확장하기 쉬우면서 변경 영향 최소화 |
| **L** | Liskov Substitution Principle | 파생 클래스는 기본 클래스를 대체할 수 있어야 한다 | 다형성 보장, 계약 준수 |
| **I** | Interface Segregation Principle | 클라이언트별로 세분화된 인터페이스를 만들어야 한다 | 불필요한 의존성 제거 |
| **D** | Dependency Inversion Principle | 고수준 모듈은 저수준 모듈에 의존하지 않아야 한다 | 결합도 감소, 테스트 용이성 |

### 어떻게 소프트웨어의 유지보수성을 높일 수 있을까?
유지보수성을 높인다는 의미는 무엇일까? 가독성이 좋게 코드를 작성하면 유지보수성이 높은 것일 수도 있지만, 설계 관점에서 코드의 유지보수성을 판단할 때는 조금 더 실무적인 아래의 세 가지 맥락을 파악한다.

1. **영향범위**: 코드 변경으로 인한 영향 범위가 어떻게 되는가?
2. **의존성**: 소프트웨어의 의존성 관리가 제대로 이뤄지고 있는가?
3. **확장성**: 쉽게 확장 가능한가?

**SOLID는 위 질문의 답을 알려주는 원칙이다.**
- SOLID 원칙을 따르는 코드가 유지보수성이 올라가는 이유는 코드 변경으로 인한 영향 범위를 축소하고, 의존성을 제대로 관리하며, 기능확장이 쉽기 때문이다.

## 📘 SOLID 원칙 상세 설명

### 1. SRP - Single Responsibility Principle (단일 책임 원칙)

#### 핵심 특성
- **하나의 변경 이유**: 클래스를 변경해야 할 이유는 단 하나여야 한다
- **액터 기반 책임**: 하나의 모듈은 하나의, 오직 하나의 액터에 대해서만 책임져야 한다
- **영향 범위 최소화**: 변경으로 인한 영향 범위를 최소화하는 것이 목적

#### 책임이란 무엇인가?
단일 책임 원칙에서 말하는 책임이란 무엇일까? 책임이라는 개념은 추상적이고 그것을 바라보는 개인이나 상황마다 다르게 해석되기 때문에 책임이 무엇이고 이를 어떻게 나눌 것인지 기준이 필요하다.

SOLID 창시자가 단일 책임 원칙에 책임을 구분할 수 있는 말을 첨언했다:

> "하나의 모듈은 하나의, 오직 하나의 액터에 대해서만 책임져야 한다."
> - 로버트 C. 마틴

단일 책임에서 말하는 책임은 **액터에 대한 책임**을 말한다.

#### 액터(Actor)의 정의
액터(actor)는 **시스템에 변경을 요청하는 이해관계자**를 의미한다:
- 비즈니스 부서(회계팀, 인사팀, 영업팀 등)
- 시스템 사용자 그룹(관리자, 일반 사용자)
- 외부 시스템이나 규제 기관

액터는 실제 사람이나 사람들의 그룹을 말하는 것이다. 코드 내에서 메시지를 요청하는 클래스를 의미하는 것이 아니다. 즉, 액터는 코드 변경의 요구사항이 발생하는 출처인 실제 사람이나 조직을 의미한다.

#### 코드 예시
```java
// SRP 위반 예시 - 하나의 클래스가 여러 부서(액터)의 기능을 담당
class Book {
    private String title;
    private String author;
    private double price;
    private int stock;
    
    // 재고 관리팀을 위한 기능
    public void updateStock(int newStock) { /* ... */ }
    
    // 마케팅팀을 위한 기능
    public void applyDiscount(double discountPercent) { /* ... */ }
    
    // 고객 서비스팀을 위한 기능
    public String getBookInfo() { /* ... */ }
}
```

재고관리팀에서 입고 날짜도 변경해달라 하고, 마케팅팀에서 마일리지에 따라 등급이 변경될 수 있게 수정해달라 하는 상황에서, 하나의 클래스에서 여러 액터를 위한 기능을 담당하고 있어 SRP를 위반한다.

```java
// SRP 준수 예시 - 각 클래스가 하나의 액터만 담당
// 책의 기본 정보만 담은 클래스
class Book {
    private String title;
    private String author;
    private double price;
}

// 재고 관리팀을 위한 클래스
class InventoryManager {
    public void updateStock(Book book, int newStock) { /* ... */ }
}

// 마케팅팀을 위한 클래스
class PriceManager {
    public double applyDiscount(Book book, double discountPercent) { /* ... */ }
}

// 고객 서비스팀을 위한 클래스
class BookInfoService {
    public String getBookInfo(Book book) { /* ... */ }
}
```

이제 각 클래스는 하나의 액터(부서)의 요청에만 대응하면 되므로, 변경 시 다른 부서에 영향을 주지 않게 된다.

#### 오해와 진실
| 오해 | 진실 |
|------|------|
| "하나의 클래스는 하나의 메서드만 가져야 한다" | 하나의 액터에 대한 책임 범위 내에서 여러 메서드를 가질 수 있다 |
| "책임은 항상 명확하게 구분된다" | 책임의 경계는 액터와 상황에 따라 달라질 수 있다 |
| "SRP를 지키면 클래스 개수가 너무 많아진다" | 적절한 책임 분리는 오히려 전체 복잡도를 줄인다 |

#### 왜 SRP를 사용해야 하는가?
1. **변경 영향 최소화**: 한 액터의 요구사항 변경이 다른 액터에게 영향을 주지 않음
2. **예상치 못한 사이드 이펙트 방지**: 서로 다른 관심사 간의 의도치 않은 결합 방지
3. **테스트 용이성**: 단일 액터에 대한 책임만 테스트하면 됨
4. **코드 이해도 향상**: 클래스의 목적이 명확해 이해하기 쉬움

#### SRP 적용이 적합한 상황
- **여러 부서가 관련된 기능**: 서로 다른 이해관계자가 변경을 요청할 수 있는 경우
- **복잡한 비즈니스 로직**: 여러 관심사가 섞여있는 대형 클래스
- **자주 변경되는 요구사항**: 변경 사항이 다른 기능에 영향을 주는 경우
- **팀 개발**: 여러 개발자가 동시에 작업하는 클래스

#### SRP와 응집도의 관계
SRP는 **높은 응집도**(cohesion)와 밀접한 관련이 있다:
- SRP를 준수하는 클래스는 단일 액터에 대한 책임만 갖기 때문에 자연스럽게 응집도가 높아진다
- 응집도가 높은 클래스는 한 가지 일을 잘하며, 이해하기 쉽고 재사용성이 높다

#### SRP 정리
> "클래스를 변경해야 할 이유는 단 하나여야 한다."
> - 로버트 C. 마틴

단일 책임 원칙의 목표:
- 클래스가 변경됐을 때 영향을 받는 액터는 하나여야 한다
- 클래스를 변경할 이유는 유일한 액터의 요구사항이 변경될 때로 제한되어야 한다

### 2. OCP - Open/Closed Principle (개방/폐쇄 원칙)

#### 핵심 특성
- **확장에 열려있음**: 새로운 기능 추가가 용이해야 한다
- **수정에 닫혀있음**: 기존 코드를 변경하지 않고 기능을 확장할 수 있어야 한다
- **추상화 활용**: 코드를 추상화된 역할에 의존하게 만드는 것이 핵심

#### OCP의 목표
> "클래스의 동작을 수정하지 않고 확장할 수 있어야 한다."
> - 로버트 C. 마틴

코드를 확장하고자 할 때 취할 수 있는 최고의 전략은 기존 코드를 아예 건드리지 않는 것이다.

**OCP 목표**: 확장하기 쉬우면서 변경으로 인한 영향 범위를 최소화하는 것이다.

#### 코드 예시
```java
// OCP 위반 예시 - 새로운 결제 방식 추가 시 기존 코드 수정 필요
class PaymentProcessor {
    public void processPayment(String type, double amount) {
        if (type.equals("CREDIT_CARD")) {
            // 신용카드 결제 처리
        } else if (type.equals("PAYPAL")) {
            // 페이팔 결제 처리
        }
        // 새로운 결제 방식 추가 시 기존 코드 수정 필요
    }
}

// OCP 적용 예시 - 추상화된 역할에 의존
interface PaymentMethod {
    void process(double amount);
}

class CreditCardPayment implements PaymentMethod {
    public void process(double amount) { /* 신용카드 결제 구현 */ }
}

class PayPalPayment implements PaymentMethod {
    public void process(double amount) { /* 페이팔 결제 구현 */ }
}

class KakaoPayPayment implements PaymentMethod {
    public void process(double amount) { /* 카카오페이 결제 구현 */ }
}

class PaymentProcessor {
    public void processPayment(PaymentMethod method, double amount) {
        method.process(amount);
        // 새로운 결제 방식 추가 시 기존 코드 변경 불필요
        // 단지 PaymentMethod 인터페이스를 구현하는 새로운 클래스만 추가하면 됨
    }
}
```

#### 오해와 진실
| 오해 | 진실 |
|------|------|
| "모든 변경 사항을 예측해서 확장점을 만들어야 한다" | 필요한 시점에 적절한 추상화를 도입하는 것이 중요 |
| "OCP를 지키면 코드가 복잡해진다" | 적절한 추상화는 오히려 코드를 단순화한다 |
| "인터페이스만 사용하면 OCP를 지킨다" | 올바른 추상화 설계가 더 중요하다 |

#### 왜 OCP를 사용해야 하는가?
1. **안정성**: 기존 코드를 변경하지 않아 버그 발생 위험 감소
2. **확장성**: 새로운 요구사항에 유연하게 대응 가능
3. **재사용성**: 기존 컴포넌트를 다양한 방식으로 활용 가능
4. **테스트 안정성**: 기존 테스트 코드에 영향을 주지 않음

#### OCP 적용이 적합한 상황
- **다양한 변형이 예상되는 기능**: 결제 방식, 알림 방식, 데이터 포맷 등
- **플러그인 구조**: 외부에서 기능을 확장할 수 있는 시스템
- **전략 패턴 적용**: 알고리즘이나 처리 방식을 교체할 수 있는 구조
- **라이브러리 설계**: 다양한 사용자 요구사항을 수용해야 하는 경우

#### OCP 정리
> "소프트웨어 개체는 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다."

코드를 추상화된 역할에 의존하게 만듦으로써 이를 달성할 수 있다. 인터페이스를 구현한다는 의미와 같다.

### 3. LSP - Liskov Substitution Principle (리스코프 치환 원칙)

#### 핵심 특성
- **치환 가능성**: 파생 클래스는 기본 클래스를 대체할 수 있어야 한다
- **계약 준수**: 기본 클래스의 계약을 파생 클래스가 제대로 치환할 수 있는지 확인
- **다형성의 기반**: 다형성 = 리스코프 치환원칙

#### LSP의 핵심 개념
> "파생 클래스는 기본 클래스를 대체할 수 있어야 한다."
> - 로버트 C. 마틴

기본 클래스의 계약을 파생 클래스가 제대로 치환할 수 있는지 확인하라는 원칙이다.

**중요한 통찰**: 
- **인터페이스는 계약이며, 테스트는 계약 명세이다**
- **다형성 = 리스코프 치환원칙**

#### 코드 예시
```java
// LSP 위반 예시
class Bird {
    public void fly() { /* 날기 구현 */ }
}

class Penguin extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("펭귄은 날 수 없습니다");
        // Bird를 Penguin으로 치환했을 때 예외 발생
        // 기본 클래스의 계약(날 수 있다)을 위반
    }
}

// LSP 적용 예시
abstract class Bird {
    public abstract void move();
}

class FlyingBird extends Bird {
    public void move() { fly(); }
    protected void fly() { /* 날기 구현 */ }
}

class SwimmingBird extends Bird {
    public void move() { swim(); }
    protected void swim() { /* 수영 구현 */ }
}

class Eagle extends FlyingBird { /* 독수리는 날 수 있음 */ }
class Penguin extends SwimmingBird { /* 펭귄은 수영할 수 있음 */ }
```

#### 오해와 진실
| 오해 | 진실 |
|------|------|
| "상속만 사용하면 LSP가 자동으로 지켜진다" | 올바른 상속 관계 설계가 필요하다 |
| "하위 클래스는 상위 클래스와 완전히 같아야 한다" | 행동 호환성이 중요하며, 구현은 다를 수 있다 |
| "LSP는 상속에만 적용된다" | 인터페이스 구현에도 동일하게 적용된다 |

#### 왜 LSP를 사용해야 하는가?
1. **다형성 보장**: 안전한 다형성 사용 가능
2. **코드 안정성**: 예상치 못한 동작으로 인한 버그 방지
3. **확장성**: 새로운 하위 클래스 추가 시 기존 코드 영향 최소화
4. **신뢰성**: 클라이언트 코드가 예상대로 동작

#### LSP 적용이 적합한 상황
- **상속 계층 설계**: 명확한 is-a 관계가 있는 경우
- **다형성 활용**: 상위 타입으로 여러 하위 타입을 다루는 경우
- **프레임워크 설계**: 확장 가능한 구조를 제공하는 경우
- **API 설계**: 일관된 인터페이스를 보장해야 하는 경우

#### LSP 정리
> "프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다."

기본 클래스의 계약을 파생 클래스가 제대로 지켜야 안전한 다형성이 가능하다.

### 4. ISP - Interface Segregation Principle (인터페이스 분리 원칙)

#### 핵심 특성
- **클라이언트 특화**: 클라이언트별로 세분화된 인터페이스를 만들어야 한다
- **불필요한 의존성 제거**: 클래스가 자신에게 필요하지 않은 인터페이스의 메서드를 구현하거나 의존하지 않아야 한다
- **인터페이스 크기 최소화**: 인터페이스의 크기를 작게 유지하고, 클래스가 필요한 기능에만 집중할 수 있도록 한다

#### ISP의 핵심 개념
> "클라이언트별로 세분화된 인터페이스를 만드세요"
> - 로버트 C. 마틴

어떤 클래스가 자신에게 필요하지 않은 인터페이스의 메서드를 구현하거나 의존하지 않아야 한다. 이를 통해 인터페이스의 크기를 작게 유지하고, 클래스가 필요한 기능에만 집중할 수 있다.

#### 코드 예시
```java
// ISP 위반 예시 - 하나의 큰 인터페이스에 모든 기능 포함
interface Worker {
    void work();
    void eat();
    void sleep();
}

class Human implements Worker {
    public void work() { /* 인간은 일할 수 있음 */ }
    public void eat() { /* 인간은 먹을 수 있음 */ }
    public void sleep() { /* 인간은 잠잘 수 있음 */ }
}

class Robot implements Worker {
    public void work() { /* 로봇은 일할 수 있음 */ }
    public void eat() { /* 로봇은 먹지 않음 - 불필요한 구현 */ }
    public void sleep() { /* 로봇은 자지 않음 - 불필요한 구현 */ }
}

// ISP 적용 예시 - 클라이언트별 세분화된 인터페이스
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

interface Sleepable {
    void sleep();
}

class Human implements Workable, Eatable, Sleepable {
    public void work() { /* 인간은 일할 수 있음 */ }
    public void eat() { /* 인간은 먹을 수 있음 */ }
    public void sleep() { /* 인간은 잠잘 수 있음 */ }
}

class Robot implements Workable {
    public void work() { /* 로봇은 일할 수 있음 */ }
    // 필요한 인터페이스만 구현 - 불필요한 의존성 제거
}
```

#### 오해와 진실
| 오해 | 진실 |
|------|------|
| "인터페이스는 무조건 작아야 한다" | 응집도가 높은 관련 기능들은 함께 있을 수 있다 |
| "모든 메서드를 별도 인터페이스로 만들어야 한다" | 논리적으로 관련된 기능은 함께 그룹화할 수 있다 |
| "ISP는 인터페이스에만 적용된다" | 클래스 설계에도 동일한 원칙이 적용된다 |

#### 왜 ISP를 사용해야 하는가?
1. **결합도 감소**: 불필요한 의존성 제거
2. **유연성 향상**: 필요한 기능만 선택적으로 구현
3. **테스트 용이성**: 작은 인터페이스는 모킹하기 쉬움
4. **가독성**: 인터페이스의 목적이 명확해짐

#### ISP 적용이 적합한 상황
- **다양한 클라이언트**: 서로 다른 요구사항을 가진 클라이언트들
- **선택적 기능**: 모든 구현체가 모든 기능을 제공하지 않는 경우
- **역할 분리**: 하나의 클래스가 여러 역할을 수행하는 경우
- **라이브러리 설계**: 사용자가 필요한 기능만 의존하도록 하는 경우

#### ISP 정리
> "클라이언트는 자신이 사용하지 않는 메서드에 의존관계를 맺으면 안 된다."

클라이언트별로 세분화된 인터페이스를 제공하여 불필요한 의존성을 제거하고, 각 클래스가 필요한 기능에만 집중할 수 있도록 한다.

### 5. DIP - Dependency Inversion Principle (의존성 역전 원칙)

#### 핵심 특성
- **추상화에 의존**: 구체적인 것이 추상적인 것에 의존해야 한다
- **고수준 모듈 보호**: 고수준 모듈이 저수준 모듈에 의존하지 않아야 한다
- **의존성 주입**: 외부에서 의존성을 주입받는 구조

#### 코드 예시
```java
// DIP 위반 예시
class OrderService {
    private MySQLOrderRepository repository; // 구체 클래스에 의존
    
    public OrderService() {
        this.repository = new MySQLOrderRepository(); // 직접 생성
    }
    
    public void createOrder(Order order) {
        repository.save(order);
    }
}

// DIP 적용 예시
interface OrderRepository {
    void save(Order order);
    Order findById(Long id);
}

class OrderService {
    private final OrderRepository repository; // 추상화에 의존
    
    public OrderService(OrderRepository repository) { // 의존성 주입
        this.repository = repository;
    }
    
    public void createOrder(Order order) {
        repository.save(order);
    }
}

class MySQLOrderRepository implements OrderRepository {
    public void save(Order order) { /* MySQL 구현 */ }
    public Order findById(Long id) { /* MySQL 구현 */ }
}
```

#### 오해와 진실
| 오해 | 진실 |
|------|------|
| "모든 클래스를 인터페이스로 만들어야 한다" | 변경 가능성이 있는 부분에 선택적으로 적용 |
| "DIP는 의존성 주입과 같다" | 의존성 주입은 DIP를 실현하는 방법 중 하나 |
| "고수준 모듈은 저수준 모듈을 알면 안 된다" | 추상화를 통해 알 수 있지만, 구체 구현에 의존하면 안 된다 |

#### 왜 DIP를 사용해야 하는가?
1. **유연성**: 구현체 교체가 용이함
2. **테스트 용이성**: 모킹을 통한 단위 테스트 가능
3. **확장성**: 새로운 구현체 추가 시 기존 코드 변경 불필요
4. **결합도 감소**: 모듈 간 의존성 최소화

#### DIP 적용이 적합한 상황
- **외부 시스템 연동**: 데이터베이스, 외부 API 등
- **변경 가능성이 높은 부분**: 알고리즘, 정책 등
- **테스트가 중요한 코드**: 비즈니스 로직 등
- **다양한 구현이 필요한 경우**: 환경별 다른 구현체 사용

#### DIP 정리
> "고수준 모듈은 저수준 모듈에 의존해서는 안 된다. 둘 다 추상화에 의존해야 한다."

## 04장 정리

SOLID 원칙은 객체지향 설계의 가이드 같은 존재이며, 각 원칙이 서로 보완하며 더 나은 소프트웨어 설계를 가능하게 한다.

### 핵심 메시지
- **SRP**: 하나의 액터에 대한 책임만 가지도록 하여 변경의 영향을 최소화
- **OCP**: 추상화된 역할에 의존하여 확장은 쉽고 수정은 최소화
- **LSP**: 기본 클래스의 계약을 준수하여 안전한 다형성 보장
- **ISP**: 클라이언트별 세분화된 인터페이스로 불필요한 의존성 제거
- **DIP**: 추상화에 의존하여 유연하고 테스트 가능한 구조 구축

### SOLID가 해결하는 유지보수성의 핵심 질문
SOLID 원칙은 설계 관점에서 유지보수성을 판단하는 세 가지 핵심 질문에 대한 답을 제시한다:

1. **영향범위**: 코드 변경으로 인한 영향 범위가 어떻게 되는가?
   - SRP: 액터별 책임 분리로 변경 영향 최소화
   - OCP: 기존 코드 수정 없이 확장 가능

2. **의존성**: 소프트웨어의 의존성 관리가 제대로 이뤄지고 있는가?
   - LSP: 계약 기반 안전한 의존성 관리
   - ISP: 불필요한 의존성 제거
   - DIP: 추상화를 통한 의존성 역전

3. **확장성**: 쉽게 확장 가능한가?
   - OCP: 수정 없는 확장 구조
   - DIP: 구현체 교체 용이성

### 실무 적용 시 고려사항
- **점진적 적용**: 모든 원칙을 한 번에 적용하려 하지 말고 필요한 부분부터 점진적으로 적용
- **과도한 추상화 주의**: 불필요한 추상화는 오히려 복잡성을 증가시킬 수 있음
- **팀 공감대 형성**: 팀원들과 SOLID 원칙에 대한 공통 이해 필요
- **리팩토링 관점**: 기존 코드를 개선할 때 SOLID 원칙을 적용하는 것이 효과적

### 실무 적용 팁
- 클래스를 설계할 때 "이 기능은 누구(어떤 액터)를 위한 것인가?"를 항상 질문하자
- 코드 리뷰 시 "이 변경이 다른 이해관계자에게 영향을 미치는가?"를 확인하자
- 인터페이스는 계약이며, 테스트는 계약 명세라는 것을 기억하자
- 추상화된 역할에 의존하는 구조를 만들어 확장성을 높이자

> "SOLID 원칙은 변경에 유연하게 대응할 수 있는 소프트웨어를 만들기 위한 지침이다. 원칙 자체보다는 원칙이 추구하는 가치(영향범위 최소화, 의존성 관리, 확장성)를 이해하고 적용하는 것이 중요하다."

### 참고
- SOLID 원칙은 로버트 C 마틴이 정리한 객체지향 설계 원칙이다.
- 각 원칙은 독립적으로 적용 가능하지만, 함께 사용할 때 시너지 효과 발생한다.
- 실무에서는 100% 완벽한 적용보다는 프로젝트 상황에 맞게 적절한 수준의 적용이 중요하다.
