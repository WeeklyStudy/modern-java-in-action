## Q1. **의무 체인 패턴의 장단점은 무엇이고, 어떤 상황에서 활용되는가?**

### 1. 의무 체인 패턴이란?

- 책임 연쇄 패턴(Chain Of Responsibility Pattern, COR)이라고도 불리기도 한다.
- 클라이어트 요청에 대한 처리를 **여러 처리 객체들(핸들러, handler)** 로 나누고, 이들을 **사슬(chain)** 처럼 연결하여 집합 안에서 연쇄적으로 처리하는 행동 패턴이다.
- 요청을 받으면 각 핸들러는 요청을 처리할 수 있는 부분은 처리하고, 다음 핸들러로 처리에 대한 책임을 전가하는 구조다.
- 한마디로 ‘Chain Of Responsibility’의 의미는 어떤 요청이 들어왔을 때, **그 요청을 처리할 책임**을 처리할 수 있는 **적당한 대상에게 넘겨준다**는 의미다.
- Exception을 throw하는 것도 비슷한 원리이지만, Exception뿐만 아니라 다양한 사건에 대해 적합한 처리자를 찾아갈 수 있도록 하는 기법이다.
- Java에서 `try - catch` 구문도 의무 체인 패턴에 해당한다.

### 2. 의무 체인 패턴 장단점

- 장점:
    - 각 핸들러들의 결합도 감소 → 확장 용이
        - 각 핸들러들을 **독립적으로 구성**할 수 있고, 사슬에 들어가는 핸들러를 바꾸거나 순서를 바꿈으로써 각 **객체에 따라 역할을 동적으로 추가 및 삭제**할 수 있다.
        - 또한, 여러 클래스간 걸쳐 의무 체인(Chain Of Responsibility)이 이루어질 수 있기 때문에, **다른 클래스에 대해 낮은 결합도**로 처리할 수 있다.
- 단점:
    - 처리 순서의 복잡성
        - 작업이 여러 객체로 분산되기 때문에 **체인 내부에서의 순서**를 잘 조정해야 한다. 체인 순서를 혼동한다면 잘못된 결과 혹은 무한 루프에 빠질 수도 있다.

### 3. 의무 체인 패턴 활용

- 여러 처리 단계가 있는 경우
    - 각 단계로 나눌 수 있는 작업을 하나의 메서드로 구성하면, 각 작업 별로 결합도가 높아져 유연성이 떨어진다. 이 때, 의무 체인 패턴을 활용할 수 있다.
- 작업 처리 로직의 분리가 필요한 경우
    - 작업 처리 로직이 복잡하거나 다양한 조건에 따라 분기되는 경우, 의무 체인 패턴을 활용하여 작업 처리 로직을 각 단계로 분리하고 간단한 조합으로 구성할 수 있다.

### 4. 의무 체인 패턴 예제

- [의무 체인 패턴 코드 예제 1 - 주문(order) 프로세스](https://gitlab.com/mskim0ct/tutorial/-/tree/main/src/com/fastcampus/chapter10/chanofresponsibility)
  - 의무 체인 패턴에서 널리 쓰이는 구조는 아래와 같이 ConcreteHandler 클래스가 하위 클래스들과 공통된 Handler 인터페이스(혹은 추상클래스)를 구현(혹은 상속)하는 것이나, 자바8 이후에는 해당 예제와 같이 하위 클래스로 구현하지 않고 람다식을 통해 코드를 간결하게 작성할 수 있다.
    - [Java 8 이전] 일반적인 구조
       <img src="https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/6be5885b-09cd-471a-a4a5-b32b81af1161" width="300">
      - Base Handler는 모든 Handler 클래스에 공통적인 상용구 코드를 넣을 수 있는 선택적 클래스이다.
    - [Java 8] 람다식을 통해 익명함수 선언 및 구현
       ```java
       public class Main {

           public static void main(String[] args) {
               //프로세스 시작
               OrderProcessStep initializeStep = new OrderProcessStep(order -> {
                   if (order.getStatus() == OrderStatus.CREATED) {
                       System.out.println("start processing order " + order.getId());
                       order.setStatus(OrderStatus.IN_PROGRESS);
                   }
               });
               //총 합을 구함
               OrderProcessStep setOrderAmountStep = new OrderProcessStep(order -> {
                   if (order.getStatus() == OrderStatus.IN_PROGRESS) {
                       System.out.println("Setting amount of order " + order.getId());
                       order.setAmount(order.getOrderLines().stream()
                               .map(OrderLine::getAmount)
                               .reduce(BigDecimal.ZERO, BigDecimal::add));
                   }
               }); 
        ...
       ```
  
  - OrderProcessStep 클래스는 생성자에 Consumer<Order> 인터페이스를 전달하여 작업을 처리하는 기능을 정의하고 있다. 
  - 이렇게 정의된 Consumer<Order> 인터페이스는 람다 표현식으로 구현되어 해당 클래스를 확장하거나 인터페이스를 구현하지 않고도 객체를 생성하고 작업을 처리할 수 있게 해준다.
- [의무 체인 패턴 코드 예제 2 - 데이터 정합성을 검증하기 위한 프로세스](https://www.nextree.co.kr/p2533/)

### 5. 결론

- 기존 코드에서 여러 처리 단계를 한 곳에서 중앙집권적으로 책임을 지고 있었다면, `각 단계 별 핸들러를 생성`하여 `책임을 분리`하고, `핸들러들을 연결`하면 유연하게 독립적인 코드로 수정할 수 있다.
    - 특히 `중첩된 여러 개의 조건문(if문)`을 의무 체인 패턴으로 변환하면, 초기에는 코드 양이 증가하고 복잡해 보일 수 있지만, 나중에는 확장 및 유지보수가 더 편리하고 유연한 구조가 된다.

### References

- [[Design Pattern] Chain of Responsibility pattern](https://tbmaster.tistory.com/m/135)
- [디자인패턴: Chain of Responsibility를 이용한 디멘션 검증](https://www.nextree.co.kr/p2533/)
- [디자인 패턴(Design Pattern) : 책임 연쇄 패턴 / 커맨드 패턴](https://www.robotstory.co.kr/raspberry/?mode=view&board_pid=81)
- [💠 Chain Of Responsibility 패턴 - 완벽 마스터하기](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-Chain-Of-Responsibility-%ED%8C%A8%ED%84%B4-%EC%99%84%EB%B2%BD-%EB%A7%88%EC%8A%A4%ED%84%B0%ED%95%98%EA%B8%B0#chain_of_responsibility_pattern)
- [[Dive Into Design Patterns] Chain of Responsibility](https://refactoring.guru/design-patterns/chain-of-responsibility)

## Q2.**옵저버 패턴이란?**

### 1. 옵저버 패턴이란?

- Pub/Sub(발행/구독) 모델이라고 불리기도 한다.
- 어떤 객체에 이벤트가 발생했을 때, 객체의 상태 변화를 관찰하는 관찰자들(observers)에게 통지하도록 하는 디자인 패턴이다.
    - 프로그래밍에서 사용하는 단어는 ‘관찰자’이지만, **갱신을 위한 정보를 전달받기 위해 기다리는 대상자**들이라고 생각하면 된다.
- 다른 디자인 패턴과 다르게 **일대다(one-to-many) 의존성**을 가지는데, 주로 분산 이벤트 핸들링 시스템을 구현하는 데 사용된다.
    - 특히 MVC 패턴에서 주로 사용되는 패턴 중 하나로, Model의 변화에 따라 View가 자동으로 갱신되어야 하는 등 Model과 View 상호작용을 담당하는 부분에서 적용된다.

### 2. 옵저버 패턴 장단점

- 장점:
    - 낮은 결합도
        
        <img src="https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/557e1157-7e3b-40e2-bbcc-0a8cfe943535" width="700">

        
        - 통보 대상 객체의 관리를 Subject 클래스와 Observer 인터페이스(혹은 추상클래스)로 일반화한다.
        - 이를 통해 데이터 변경을 통보하는 클래스(ConcreteSubject)는 통보 대상 클래스나 객체(ConcreteObserver)에 대한 의존성을 없애고 결합도를 낮출 수 있다.
    - 확장
        - 발행자의 코드를 변경하지 않고도 새 구독자 클래스를 도입할 수 있어 개방 폐쇄 원칙(OCP)을 준수한다.
    - 감지 및 갱신
        - 특정 객체의 상태 변경을 주기적으로 조회하지 않고 자동으로 감지 및 갱신할 수 있다.
- 단점:
    - 복잡도 증가
        - 옵저버 패턴을 자주 구성하면 구조와 동작을 알아보기 힘들어져 코드 복잡도가 증가한다.
    - 메모리 누수 가능성
        - 다수의 Observer 객체 등록 후 해지하지 않으면(Observer 목록 컬렉션에서 참조값을 가지고 있다면) GC 대상이 되지 않아 메모리 누수가 발생할 수도 있다.

### 3. 옵저버 패턴 활용

- 앱이 **한정된 시간**, **특정한 케이스**에만 다른 객체를 관찰해야 하는 경우
- 대상 객체의 상태가 변경될 때마다 **다른 객체의 동작을 트리거**해야 할때

### 4.  옵저버 패턴 예제

- [날씨 API로부터 온습도 알림 받기](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%98%B5%EC%A0%80%EB%B2%84Observer-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90)
- [데이터 변화에 대한 화면 갱신 처리](https://doohyun.tistory.com/81)

### 5. 결론

- 옵저버 패턴은 `상태가 변화되는 객체` 와 이 객체의 상태에 `의존성이 있는 객체`, 이 두 객체가 직접적으로 참조되지 않고 중간에 관찰자 객체를 둠으로써 느슨한 연결구조를 설계한다.
- 특히 `상태가 변화되는 객체`와 그 상태에 `의존성이 있는 객체` 간의 `일대다 관계`가 필요한 경우, 옵저버 패턴을 활용하여 DIP(Dependency Inversion Principle) 원칙을 준수하며 유연한 구조를 구성할 수 있다.

### References

- [💠 옵저버(Observer) 패턴 - 완벽 마스터하기](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%98%B5%EC%A0%80%EB%B2%84Observer-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90#%ED%8C%A8%ED%84%B4_%EC%9E%A5%EC%A0%90)
- [[디자인 패턴] 행동 패턴 - 옵저버(관찰자) 패턴 (Observer Pattern)](https://thalals.tistory.com/432)
- [[CHAPTER 5] 실무 활용 패턴 (하) [Observer 패턴] + 추가내용](https://doohyun.tistory.com/81)

## Q3. **디자인 패턴들을 어떤 방법으로 분류할 수 있을까?**

### 1. GoF 디자인 패턴 분류 기준

- GoF 디자인 패턴을 분류하는 기준은 두 가지이다.
- 목적(생성, 구조, 행동)과 범위(클래스에 적용, 객체에 적용)에 따라 분류할 수 있다.
    
    <img src="https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/ea75f1bc-7b20-44d3-81ab-177cfe98b7f1" width="600">


### 2. 목적에 따라 분류하기

- 생성 패턴(Creational Patterns)
    - `객체의 생성과 조합을 캡슐화`해 특정 객체가 생성되거나 변경되어도 프로그램 구조에 영향을 크게 받지 않도록 유연성을 제공한다.
        - **싱글톤 패턴(Singleton)** : 클래스의 인스턴스가 하나임을 보장하고 접근할 수 있는 전역적인 접근점을 제공하는 패턴으로, 디자인 패턴의 가장 많이 알려진 패턴이다.
        - **추상팩토리 패턴(Abstract Factory) :** 구체적인 클래스를 지정하지 않고 관련성이 있거나, 독립적인 객체들을 생성하기 위한 인터페이스를 제공하는 패턴이다.
        - **빌더 패턴(Builder) :** 복합 객체의 생성과정과 표현과정을 분리시켜 동일한 생성과정에서 다양한 표현을 생성할 수 있는 패턴이다.
        - **팩토리 메서드 패턴(Factory Method) :** 객체를 생성하는 인터페이스는 정의해놓고, 인스턴스를 만드는 클래스는 서브클래스에서 결정하도록 하는 패턴이다.
        - **원형 패턴(Prototype) :** 생성할 객체의 종류를 명시하는 데 원형이 되는 예시물을 이용하고 새로운 객체를 이 원형들을 복사함으로써 생성하는 패턴이다.
- 구조 패턴(Structural Patterns)
    - `클래스나 객체를 조합`해 더 큰 구조를 만드는 패턴이다.
        - **적응자 패턴(Adapter) :** 클래스의 인터페이스를 사용자가 기대하는 다른 인터페이스로 변환하는 패턴으로, 호환성이 없는 인터페이스 때문에 함께 동작할 수 없는 클래스들이 함께 작동하도록 해주는 패턴이다.
        - **브리지 패턴(Bridge) :** 구현부에 추상층을 분리하여 각자 독립적으로 변형할 수 있도록 하는 패턴이다.
        - **컴포지트 패턴(Composite)** : 객체들의 관계를 트리 구조로 구성하여 부분-전체 계층을 표현하는 패턴으로, 사용자가 단일 / 복합객체 모두 동일하게 다루도록 하는 패턴이다.
        - **데코레이터 패턴(Decorator) :** 주어진 상황 및 용도에 따라 어떤 객체에 책임을 덧붙이는 패턴으로, 기능확장이 필요할 때 서브클래스 대신 쓸 수 있는 대안이 될 수 있다.
        - **퍼사드 패턴(Facade) :** 서브시스템에 있는 인터페이스 집합에 통합된 하나의 인터페이스를 제공한다. 서브시스템을 좀 더 쉽게 사용하기 위해 고수준의 인터페이스를 정의한다.
        - **프록시 패턴(Proxy) :** 어떤 다른 객체로 접근하는 것을 통제하기 위해 그 객체의 매니저 또는 자리 채움자를 제공하는 패턴이다.
- 행위 패턴(Behavioral Patterns)
    - 객체나 클래스 사이의 `알고리즘`이나 `책임 분배`에 관련된 패턴이다. 한 객체가 혼자 수행할 수 없는 작업을 여러개의 객체로 어떻게 분배하는지, 또 그렇게 하면서도 객체 사이의 결합도를 최소화하는것에 중점을 두는 방식이다.
        - **옵저버 패턴(Observer) :** 객체들 사이에 1 : N 의 의존관계를 정의하여 어떤 객체의 상태가 변할 때, 의존관계에 있는 모든 객체들이 통지받고 자동으로 갱신될 수 있게 만드는 패턴이다.
        - **상태 패턴(State) :** 객체의 내부 상태가 변경될 때 행동을 변경하도록 허락한다. 객체는 자신의 클래스가 변경되는 것처럼 보이게 된다.
        - **전략 패턴(Strategy) :** 동일 계열의 알고리즘들을 정의하고, 각각 캡슐화하며 이들을 상호교환 가능하도록 만드는 것이다. 알고리즘을 사용하는 사용자로부터 독립적으로 알고리즘이 변경될 수 있도록 하는 패턴이다.
        - **템플릿 패턴(Template) :** 객체의 연산에서 알고리즘의 뼈대만 정의하고, 나머지는 서브클래스에서 이루어지게 하는 패턴이다. 템플릿패턴은 알고리즘의 구조는 변경하지 않고 알고리즘의 각 단계를 서브클래스에서 재정의하게 된다.
        - **방문자 패턴(Visitor) :** 객체구조를 이루는 원소에 대해 수행할 연산을 표현한다. 방문자는 연산에 적용할 원소의 클래스를 변경하지 않고 새로운 연산을 재정의 할 수 있다.
        - **책임 연쇄 패턴(Chain of Responsibility) :** 요청을 처리하는 기회를 하나 이상의 객체에 부여하여 요청을 보내는 쪽과 받는 쪽의 결합을 피하는 패턴이다. 요청을 받는 객체를 연쇄적으로 묶고 객체를 처리할 수 있을 때까지 요청을 전달한다.
        - **커맨드 패턴(Command) :** 요청을 객체로 캡슐화하여 서로 다른 사용자의 매개변수화, 요청 저장 또는 로깅, 연산의 취소를 지원하게 만드는 패턴이다.
        - **인터프리터 패턴(Interpreter) :** 주어진 언어에 대해서 문법을 위한 표현수단을 정의하고, 해당 언어로 된 문장을 해석하는 해석기를 사용하는 패턴이다.
        - **이터레이터 패턴(Iterator) :** 내부 표현부를 노출하지 않고 어떤 객체 집합의 원소들을 순차적으로 접근할 수 있는 방법을 제공하는 패턴이다.
        - **중재자 패턴(Mediator) :** 한 집합에 속해있는 객체들의 상호 작용을 캡슐화하는 객체를 정의하는 패턴이다. 중재자는 객체들이 직접 서로 참조하지 않도록함으로써 객체들간의 느슨한 연결을 촉진시키며 객체들의 상호작용을 독립적으로 다양화 시킬 수 있도록 해준다.

### 3. 구체적인 상황에서 활용할 수 있는 패턴
- **객체가 특정 클래스에 종속되지 않도록 하기**
    - 추상팩토리 패턴(Abstract Factory)
    - 팩토리 메서드 패턴(Factory Method)
    - 원형 패턴(Prototype)
- **요청 처리 방법이 특정 함수에 종속되지 않도록 하기**
    - 책임 연쇄 패턴(Chain of Responsibility)
    - 커맨드 패턴(Command)
- **HW와 SW에 종속되지 않도록 하기**
    - 추상팩토리 패턴(Abstract Factory)
    - 브리지 패턴(Bridge)
- **변경 가능성이 있는 알고리즘은 분리하기**
    - 빌더 패턴(Builder)
    - 이터레이터 패턴(Iterator)
    - 전략 패턴(Strategy)
    - 템플릿 패턴(Template)
    - 방문자 패턴(Visitor)
- **결합도 낮추기**
    - 추상팩토리 패턴(Abstract Factory)
    - 브리지 패턴(Bridge)
    - 책임 연쇄 패턴(Chain of Responsibility)
    - 커맨드 패턴(Command)
    - 퍼사드 패턴(Facade)
    - 중재자 패턴(Mediator)
    - 옵저버 패턴(Observer)
- **객체 합성과 위임으로 단순 상속보다 유연하게 설계하기**
    - 브리지 패턴(Bridge)
    - 책임 연쇄 패턴(Chain of Responsibility)
    - 데코레이터 패턴(Decorator)
    - 옵저버 패턴(Observer)
    - 전략 패턴(Strategy)
- **특정 클래스 변경을 위해 다수 클래스를 변경해야 할 때**
    - 적응자 패턴(Adapter or Wrapper)
    - 데코레이터 패턴(Decorator)
    - 방문자 패턴(Visitor)

### 4. 디자인 패턴들의 관계

- 디자인 패턴들은 아래와 같은 관계도를 가진다.

    <img src="https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/f6e7b574-5da4-4152-9570-50d65deb23d1" width="650">

    
- 일부 패턴은 대부분 같이 사용하기도 하고, 어떤 패턴은 다른 패턴의 대안이 되기도 하고, 어떤 패턴들은 의도는 다르지만 매우 유사한 구조를 가지기도 한다.
- 따라서, 디자인 패턴들간의 관계를 파악하고, 의도에 맞게 올바르게 사용해야 한다.
  - [각 패턴의 설명과 다른 패턴과의 관계에 관한 참고 자료 *](https://refactoring.guru/ko/design-patterns/catalog)

### References

- [디자인패턴의 조직화, 관계도](https://soopiri.tistory.com/6)
- [디자인패턴간의 분류, 관계](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=sorkelf&logNo=40135225311)
- [[Design Pattern] 디자인 패턴 정의와 종류에 대하여](https://coding-factory.tistory.com/708)
- [[Design Pattern] GoF(Gang of Four) 디자인 패턴](https://4z7l.github.io/2020/12/25/design_pattern_GoF.html)
- [디자인 패턴(Design patterns) - 적절한 디자인패턴 선택방법과 사용법](https://jade314.tistory.com/entry/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4Design-patterns-Design-Patterns-Elements-of-Reusable-Object-Oriented-Software#--%C-%A-%--%EB%--%--%EC%-E%--%EC%-D%B-%--%ED%-C%A-%ED%--%B-%EC%-D%--%C-%A-%EC%A-%B-%EC%-E%AC%--%EC%-D%--%EB%-F%---Intent-%C-%A-%EA%B-%A-%EB%A-%A-%ED%--%--%EC%-E%--)
