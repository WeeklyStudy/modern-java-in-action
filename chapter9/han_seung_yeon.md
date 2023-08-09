# https://github.com/WeeklyStudy/modern-java-in-action/issues/23 옵저버 패턴이란?
    
## 💡옵저버 패턴(Observer Pattern)이란?

> 관찰 대상자의 상태가 변할 때마다 관찰 대상자가 직접 목록의 관찰자에게 통지하고 관찰자는 알림을 받아 조치를 취하는 행동 패턴이다.
> 

## 💡옵저버 패턴 구조
![Untitled (6)](https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/459a8a9f-1417-46f4-aa4a-58c5d9b98ffd)


- `Subject` : 관찰 대상자를 정의하는 인터페이스(상태가 변경되는 대상)
- `ConcreteSubject` : Subject 를 구현하는 클래스(관찰 대상자/발행자)
    - `List<Observer> observers` : `Subject` 를 관찰하는 `Observer` 들을 관리하는 컬렉션
    - `registerObserver(Observer o)` : `Observer` 를 리스트에 등록하는 메서드
    - `removeObserver(Observer o)` : `Observer` 를 리스트에서 삭제하는 메서드
    - `notifyObserver()` : `Subject` 의 상태가 변경되었을 때 목록의 `Observer` 들에게 알림을 보내는 메서드
- `Observer` : 구독자들을 묶는 인터페이스(상태 변화를 감지하는 대상)
- `ConcreteObserver` : Observer 를 구현하는 클래스(관찰자/구독자/리스너)
    - `update()` : `Subject` 의 상태 변화가 감지됐을 때 처리할 동작을 정의한 메서드

## 💡옵저버 패턴 데이터 전송 방법
![Untitled (7)](https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/d0998274-579b-4a6e-96df-278104ed82bb)


### 1. push 방식

- `Subject` 가 `Observer` 로 데이터를 보내는 방식

### 2. pull 방식

- `Observer` 에서 `Subject` 의 데이터를 가져오는 방식

## 💡옵저버 패턴 예제[[여기](https://flowarc.tistory.com/entry/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%98%B5%EC%A0%80%EB%B2%84-%ED%8C%A8%ED%84%B4Observer-Pattern)]

### 1. 구조
![Untitled (9)](https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/e7162c93-966a-4f78-91c8-2e682343e87f)

- `Publisher` : 발행자들을 묶는 인터페이스
- `NewsMachine` : 발행자 클래스
- `Observer` : 구독자들을 묶는 인터페이스
- `AnnualSubscriber` / `EventSubscriber` : 구독자 클래스

### 2. 흐름

1. `연간 구독자`와 `이벤트 구독자`가 뉴스를 구독한다. == `뉴스 머신` 옵저버 목록에 `연간 구독자`와 `이벤트 구독자`가 추가된다.
2. `뉴스 머신` 이 새로운 뉴스를 업데이트한다.
3. `뉴스 머신` 이 `연간 구독자`와 `이벤트 구독자`에게 새로운 뉴스에 대한 알림을 보낸다.
4. `연간 구독자` 와 `이벤트 구독자` 는 뉴스 정보를 출력한다.

## 💡옵저버 패턴의 장단점

### 1. 장점

1. 실시간으로 `Subject` 의 상태 변경을 `Observer` 가 감지할 수 있다.
2. 상태를 변경하는 객체(`Subject`)와 변경을 감지하는 객체(`Observer`)가 느슨하게 결합되어 있다.
    - 인터페이스를 의존하여 `Subject` 나 `Observer` 가 바뀌더라도 서로에게 영향을 미치지 않는다.
    - `Subject` 를 변경하지 않고 새로운 `Observer` 를 추가할 수 있다. → `OCP(개방 폐쇄 원칙)` 만족

> 📍**느슨한 결합(Loose Coupling)**
> 
> 
> 다른 클래스를 직접적으로 사용하는 클래스의 의존성을 줄이는 것이다. 즉, 두 객체가 상호작용을 하긴하지만 서로에 대해 잘 모른다.
> 

### 2. 단점

1. `Observer` 에게 알림이 가는 순서를 보장할 수 없습니다. → 더 조사가 필요함
2. `observer` 패턴을 많이 사용하게 되면 관리가 어려울 수 있다.
3. 다수의 `Observer` 객체를 등록하고 해지하지 않는다면 메모리 누수가 발생할 수 있다.

## 💡정리

- 옵저버 패턴에서는 `1:N(일대다)` 관계를 정의한다. (1 → subject, N → observer)
- 인터페이스를 이용해 `Subject` 와 `Observer` 간에 느슨한 결합을 유지한다.
- 옵저버 패턴은 `push` 방식과 `pull` 방식으로 구현할 수 있다.
- 자바에서 `Observable` 클래스와 `Observer` 인터페이스를 제공한다.

## Reference

- 옵저버 패턴
    - [[디자인패턴] 옵저버 패턴 (Observer Pattern) 아주 간단하게 정리해보기](https://pjh3749.tistory.com/266)
    - [옵저버(Observer) 패턴 - 완벽 마스터하기*](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%98%B5%EC%A0%80%EB%B2%84Observer-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90)
- 느슨한 결합
    - [[iOS]옵저버 패턴(Observer Pattern)](https://jiseok-zip.tistory.com/entry/iOS%EC%98%B5%EC%A0%80%EB%B2%84-%ED%8C%A8%ED%84%B4Observer-Pattern)
- 옵저버 패턴 예제
    - [디자인패턴 - 옵저버 패턴(Observer Pattern)*](https://flowarc.tistory.com/entry/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%98%B5%EC%A0%80%EB%B2%84-%ED%8C%A8%ED%84%B4Observer-Pattern)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/24 디자인 패턴들을 어떤 방법으로 분류할 수 있을까?
    
## 💡디자인 패턴 종류

디자인 패턴(Design Pattern)은 소프트웨어 공학의 소프트웨어 설계에서 공통으로 발생하는 문제에 대해 자주 쓰이는 설계 방법을 정리한 패턴이다.

디자인 패턴 23개를 크게 생성(5), 구조(7), 행위(11) 3가지로 분류할 수 있다.

### 1. 생성 패턴(****Creational Pattern****)

> 객체 생성에 관련된 패턴이다. 객체의 생성과 조합을 캡슐화하여 특정 객체가 생성되거나 변경되어도 프로그램 구조에 영향을 크게 받지 않도록 유연성을 제공한다.
> 
1. Singleton(싱글톤) : 객체의 인스턴스가 오직 1개만 생성되게 하는 패턴
2. Abstract Factory(추상 팩토리) : 구체적인 클래스를 지정하지 않고 제품군을 생성하기 위한 패턴
3. Builder(빌더) : 복잡한 객체의 생성 과정과 표현 방법을 분리하여 다양한 구성의 인스턴스를 만드는 패턴
4. Factory Method(팩토리 메서드) : 객체 생성을 위한 인터페이스를 정의하고 인스턴스 생성은 서브 클래스가 결정하는 패턴
5. Prototype(프로토타입) : 기존 인스턴스를 복사하여 새로운 인스턴스를 만드는 패턴

### 2. 구조 패턴****(Structural Pattern)[](https://www.devkuma.com/docs/design-pattern/intro-basic/#%EA%B5%AC%EC%A1%B0-%ED%8C%A8%ED%84%B4-structural-pattern)**

> 클래스나 객체를 조합해 더 큰 구조를 만드는 패턴이다.
> 
1. Adapter(어댑터) : 한 클래스의 인터페이스를 클라이언트에서 사용하고자 하는 인터페이스로 변환하는 패턴
2. Bridge(브리지) : 구현과 추상(기능)을 분리하여 각자 독립적으로 변형할 수 있게 하는 패턴
3. Decorator(데코레이터) : 주어진 상황에 따라 어떤 객체에 책임(기능)을 동적으로 추가하는 패턴
4. Facade(퍼사드) : 서브 시스템의 인터페이스 기능을 묶어 통합 인터페이스를 제공하는 패턴
5. Proxy(프록시) : 대리인이 대신 일을 처리하는 패턴
6. Composite(컴포지트) : 객체를 트리 구조로 구성해 부분-전체 계층을 표현하는 패턴
7. Flyweight(플라이웨이트) : 대량의 인스턴스를 공유하여 메모리 낭비를 줄이는 패턴

### 3. 행위 패턴****(Behavioral Pattern)[](https://www.devkuma.com/docs/design-pattern/intro-basic/#%ED%96%89%EC%9C%84-%ED%8C%A8%ED%84%B4-behavioral-pattern)**

> 객체나 클래스 사이의 알고리즘(상호작용 방법)이나 책임 분배에 관련된 패턴이다.
> 
1. Chain of Responsible(책임 연쇄) : 클라이언트의 요청을 처리하는 객체들을 체인으로 연결하는 패턴
2. Command(커맨드) : 객체의 행위(메서드)를 클래스로 만들어 캡슐화하는 패턴
3. Interpreter(인터프리터) : 문법 규칙을 클래스화한 패턴
4. Iterator(반복자) : 내부 구현을 노출하지 않고 집합의 모든 요소를 순회하는 패턴
5. Mediator(중재자) : 객체들 간의 상호작용을 캡슐화하여 하나의 클래스에 위임하여 처리하는 패턴
6. Memento(메멘토) : 객체의 이전 상태를 저장하고 복원할 수 있게 해주는 패턴
7. Observer(옵저버) : 상태가 변할 때마다 의존하는 객체에게 알리는 패턴
8. State(상태) : 객체 상태에 따라 행위를 바꾸는 패턴
9. Strategy(전략) : 알고리즘을 캡슐화하여 실행 중에 알고리즘을 선택하는 패턴
10. Template Method(템플릿 메서드) : 기능의 뼈대와 실제 구현을 분리하는 패턴
11. Visitor(비지터) : 알고리즘을 객체 구조에서 분리하는 패턴

## Reference

- [디자인 패턴 개념과 종류*](https://velog.io/@poiuyy0420/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%A2%85%EB%A5%98)
- [디자인 패턴(Design Pattern)의 개념과 종류](https://www.devkuma.com/docs/design-pattern/intro-basic/)
- [[Design Pattern] 디자인 패턴 정의와 종류에 대하여](https://coding-factory.tistory.com/708)
- [[Design pattern] 많이 쓰는 14가지 핵심 GoF 디자인 패턴의 종류](https://m.hanbit.co.kr/channel/category/category_view.html?cms_code=CMS8616098823)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/25 의무 체인 패턴의 장단점은 무엇이고, 어떤 상황에서 활용되는가?
    
## 💡책임 연쇄 패턴(Chain Of Responsibility Pattern, COR)이란?

> 클라이언트의 요청을 처리하는 객체들을 집합(Chain)으로 만들어 결합을 느슨하게 하는 행동 패턴이다.
> 
> 
> 즉, 각 핸들러에서 요청을 처리할 수 있는지 여부를 따지고 불가하면 다음 핸들러로 떠넘기는 과정을 코드로 구현한 것이다.
> 

## 💡책임 연쇄 패턴 구조
![img1 daumcdn](https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/d8ad267e-8e9c-44ee-8cc9-49947cddbe2b)

- `Handler` : 요청을 수신하고 처리 객체들의 집합에 전달하는 인터페이스
    - field : 첫번째 핸들러 정보
- `ConcreteHandler` : 요청을 처리하는 실제 처리 객체
    - `next` : 다음 핸들러 정보
    - `ConcreteHandler1` - `ConcreteHandler2` - `…` - `ConcreteHandlerN` 과 같이 체인을 구성한다.
- `Client` : 요청을 `Handler` 에게 전달하는 객체

## 💡책임 연쇄 패턴 예제[[여기](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-Chain-Of-Responsibility-%ED%8C%A8%ED%84%B4-%EC%99%84%EB%B2%BD-%EB%A7%88%EC%8A%A4%ED%84%B0%ED%95%98%EA%B8%B0#%EC%B1%85%EC%9E%84_%EC%97%B0%EC%87%84_%ED%8C%A8%ED%84%B4%EC%9D%84_%EC%A0%81%EC%9A%A9%ED%95%9C_%EC%BD%94%EB%93%9C_%E2%9C%94%EF%B8%8F)]

url의 프로토콜, 도메인 , 포트를 파싱하는 3개의 책임으로 분리하였다.

`ProtocolHandler` → `DomainHandler` → `PortHandler`

## 💡책임 연쇄 패턴 장단점

### 1. 장점

1. 요청의 발신자와 수신자를 분리시킬 수 있다. → `단일 책임 원칙(SRP)` 만족
2. 클라이언트는 처리 객체의 체인 집합 내부의 구조를 알 필요가 없다.
3. 클라이언트 코드를 변경하지 않고 처리 객체를 추가/삭제하거나 처리 순서를 변경할 수 있다. → `개방-폐쇄 원칙(OCP)` 만족

### 2. 단점

1. 실행시 코드 흐름이 많아지면 디버깅 및 테스트가 쉽지 않다.
2. 충분한 디버깅을 거치지 않으면 집합 내부에서 무한 사이클이 발생할 수 있다.
3. 책임 연쇄로 인한 처리 지연 문제가 발생할 수 있다.

## 💡책임 연쇄 패턴을 적용하는 경우

- 특정 요청을 2개 이상의 여러 객체에서 판별하고 처리해야 할 때
- 요청의 발신자와 수신자를 분리하는 경우
- 특정 순서로 여러 핸들러를 실행해야하는 경우

## Reference

- [Chain Of Responsibility 패턴 - 완벽 마스터하기*](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-Chain-Of-Responsibility-%ED%8C%A8%ED%84%B4-%EC%99%84%EB%B2%BD-%EB%A7%88%EC%8A%A4%ED%84%B0%ED%95%98%EA%B8%B0#chain_of_responsibility_pattern)
- [[디자인 패턴] 행동 패턴 - 책임 연쇄 패턴 (Chain of Responsibility Pattern)](https://thalals.tistory.com/428)
- [책임 연쇄 패턴](https://refactoring.guru/ko/design-patterns/chain-of-responsibility)
