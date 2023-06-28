## 1. 전략 패턴의 장단점은 무엇이고 어떠한 경우에 적용하면 좋을까?
    
### 1. 전략 패턴(Strategy Pattern)이란?

> 컨텍스트(Context)에서 사용할 변경 가능한 전략(Strategy)을 인터페이스를 이용하여 캡슐화하고 필요에 따라 동적으로 변경하여 사용할 수 있도록 하는 디자인 패턴이다.
> 
- 변하지 않는 부분은 `Context` 클래스에 두고, 변하는 부분은 `Strategy` 인터페이스로 선언하고 구현체를 생성한다.
- 상속 대신 위임(조합)으로 문제를 해결하는 것이 핵심이다.
- 전략 패턴의 핵심은 `Context`는 `Strategy` 인터페이스에만 의존한다는 점이다.

### 2. 전략 패턴의 형태와 구성 요소
![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/35052d69-269d-4fa1-bc38-82c84224506f/Untitled.png)
- `전략(stragtegy)` : 여러 알고리즘을 추상화 시킨 **인터페이스**
- `전략 콘크리트(concrete strategy)` : 알고리즘(어떻게 행동하는지)의 실제 **구현체(클래스)**
- `전략 사용자(context)` : 전략을 사용하는 프로그램의 흐름
- `전략 제공자(client)` : 전략 사용자에게 **전략 콘크리트를 주입**하는 객체

### 3. 전략 패턴을 사용하지 않았을 때는?

필요한 전략에 맞추어 행동하기 위해 `if-else(분기문)`을 사용한다.

1. 기능을 확장하기 위해서 수정이 필요하다. → 개방-폐쇄 원칙(OCP) 위배
    - 새로운 기능을 추가할 때마다 메서드 내부에 `if-else` 문을 추가해야 한다.

### 3. 전략 패턴의 장점

1. 컨텍스트의 코드 변경없이 새로운 전략을 추가할 수 있다. 즉, 코드의 유지보수와 확장성이 용이하다.
    - `Context` 는 `Strategy` 인터페이스에만 의존하기 때문에 `Strategy` 구현체를 추가, 변경해도 `Context` 는 영향을 받지 않는다.
    - **개방 폐쇄의 원칙(OCP)**을 만족한다!
        - `개방 폐쇄의 원칙(OCP)` : 기존의 코드를 변경하지 않으면서 기능을 추가할 수 있어야 한다.
2. 런타임에 전략을 변경할 수 있다.
3. 알고리즘을 사용하는 코드에서 이를 구현하는 세부 정보를 분리할 수 있습니다.
4. 익명 클래스와 람다식을 사용하기 편리하다.
    - 인터페이스에 메서드가 1개만 존재할 경우, 람다식을 사용할 수 있다. 람다식을 사용할 경우, 코드가 간결해지고 익명 클래스보다 직관적이다.

### 4. 전략 패턴의 단점

1. 클라이언트에서 전략 구현체에 대해 모두 알고 있어야 하고 적절한 알고리즘을 선택하는 로직이 복잡해질 수 있다.
2. 전략 객체를 생성하고 관리하는데 추가적인 비용이 발생할 수 있다.
3. 전략을 추상화한 인터페이스가 효율적이지 않을 수 있다. (전략 구현체에서 사용하는 메서드들의 형태가 공통적이지 않은 경우)
4. 알고리즘의 변화가 없고 단순한 프로그램에 적용하면 오히려 코드의 복잡도가 올라갈 수 있다.

### 5. 결론

모든 상황에서 전략 패턴이 도움이 되는 것은 아니다.

컨텍스트에 적용되는 알고리즘이 한 두개로 간단한 경우 분기를 타는 것이 간단한 경우도 있다.

요구사항 변경이 자주 일어나거나 다양한 곳에서 알고리즘(전략)을 적용할 때 사용하면 좋다.

### Reference

- 전략 패턴
    - [전략 패턴과 템플릿 콜백 패턴](https://perenok.github.io/%EC%A0%84%EB%9E%B5-%ED%8C%A8%ED%84%B4%EA%B3%BC-%ED%85%9C%ED%94%8C%EB%A6%BF-%EC%BD%9C%EB%B0%B1-%ED%8C%A8%ED%84%B4/)
    - [전략 패턴 (Strategy Pattern)](https://hudi.blog/strategy-pattern/)
    - [2021.11.16 TIL](https://velog.io/@pbg0205/2021.11.16-TIL)
    - [저는 전략 패턴이 처음이라니까요?](https://joel-dev.site/75)
- 전략 패턴의 구성 요소
    - [[알고쓰면 좋은 디자인 패턴] 전략 패턴](https://velog.io/@vov3616/%EC%95%8C%EA%B3%A0%EC%93%B0%EB%A9%B4-%EC%A2%8B%EC%9D%80-%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%A0%84%EB%9E%B5-%ED%8C%A8%ED%84%B4)
- 전략 패턴의 장단점
    - [디자인 패턴 : 전략패턴이란?](https://velog.io/@kyle/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80)
    - [전략 패턴 (Strategy Pattern)](https://keencho.github.io/posts/strategy-pattern/)
    - [[Design Pattern] Strategy Pattern(전략 패턴)](https://gsbang.tistory.com/entry/Design-Pattern-Strategy-Pattern)
    - [전략 패턴 (Strategy Pattern)](https://velog.io/@whitecloud94/%EC%A0%84%EB%9E%B5-%ED%8C%A8%ED%84%B4-Strategy-Pattern)

## 2. 인터페이스와 추상 클래스의 차이점은 무엇이고, 어떤 경우에 인터페이스를 사용하는가?

### 1. 추상 클래스

> 하나 이상의 추상 메서드(선언부만 있고 구현부는 없는 메서드)를 포함한 클래스이다.
> 
- 실체 클래스들의 **공통적인 특성(변수, 메서드)**를 추출해서 선언한 클래스이다.
    - `실체 클래스` : 객체를 직접 생성할 수 있는 클래스
- 추상 클래스는 객체를 생성할 수 없다.
- 추상 클래스와 실체 클래스는 상속 관계다.

### 2. 추상 클래스 용도

1. 공통된 필드와 메서드를 사용하여 유지보수성을 높이고 통일성을 유지한다.
2. 실체 클래스를 구현하는데 시간이 절약된다.
    - 설계를 생각할 필요없이 추상 클래스를 상속받아 공통 필드와 메서드를 이용해 구현만 하면 된다.
3. 규격에 맞는 실체 클래스를 구현하여 변화에 유연하게 대처한다.

### 3. 인터페이스

> 다른 클래스를 작성할 때 기본이 되는 틀을 제공하면서 다른 클래스 사이의 중간 매개 역할까지 담당하는 일종의 추상 클래스이다.
> 

### 4. 인터페이스 용도

자바의 다형성을 극대화하여 개발 코드 수정을 줄이고 프로그램 유지보수성을 높이기위해 사용한다.

- 서로 관련성이 없는 클래스들을 묶어 주고 싶을 때(형제 관계) 사용한다.
- `추상 메서드` 와 `상수` 를 통해 강력한 강제성을 가지게 하여 인터페이스를 implements 한 클래스가 동일한 동작을 수행하도록 보장한다.
- 자바8부터 `default` 메서드를 허용하면서 추가 요건에 대해 유연한 대처를 할 수 있다.

### 5. 추상 클래스와 인터페이스의 차이

|  | 추상 클래스 | 인터페이스 |
| --- | --- | --- |
| 사용 키워드 | abstract | interface |
| 사용 가능 변수 | 제한 없음 | static final(상수) |
| 사용 가능 접근 제어자 | 제한 없음(public, private, protected, default) | public |
| 사용 가능 메소드 | 제한 없음 | abstract method, default method, static method, private method |
| 상속 키워드 | extends | implements |
| 다중 상속 | 불가능 | 가능(클래스에 다중 구현, 인터페이스 끼리 다중 상속) |

### 4. 결론

추상 클래스는 추상 클래스를 기본 틀으로 해서 **기능을 확장할 때** 사용한다.

인터페이스는 객체 간의 통일성을 강제하기 위한 용도로 많이 사용된다.(인터페이스를 구현한 객체들의 함수 구현을 강제하여 같은 이름의 메서드로 동일한 동작을 하도록 보장한다.)

### Reference

- 추상 클래스
    - [[JAVA] 자바 추상클래스란?](https://limkydev.tistory.com/188)
    - [추상 클래스(Abstract) 용도 완벽 이해하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4Abstract-%EC%9A%A9%EB%8F%84-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0#%EA%B7%9C%EA%B2%A9%EC%97%90_%EB%A7%9E%EB%8A%94_%EC%84%A4%EA%B3%84_%EA%B5%AC%ED%98%84)
- 인터페이스
    - [[JAVA] 자바 인터페이스란?(Interface)_이 글 하나로 박살내자](https://limkydev.tistory.com/197)
- 추상 클래스와 인터페이스의 차이
    - [인터페이스 vs 추상클래스 용도 차이점 - 완벽 이해](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-vs-%EC%B6%94%EC%83%81%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
    - [[Spring] Interface, AbstractClass의 차이점과 ServiceImpl 패턴을 쓰는 이유.](https://velog.io/@kshired/Spring-Java-Interface-AbstractClass%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90%EA%B3%BC-ServiceImpl-%ED%8C%A8%ED%84%B4%EC%9D%84-%EC%93%B0%EB%8A%94-%EC%9D%B4%EC%9C%A0)

## 3. Comparator와 Comparable의 각 특징과 차이점은 무엇일까?
    
### 1. Comparable

> Comparable 은 **객체가 임의의 기준으로 정렬될 수 있도록** 만들기 위해 사용되는 인터페이스이다.
> 
- `Comparable` 인터페이스를 구현하는 클래스는 `compareTo(T o)` 메서드를 오버라이딩 해야한다.
- `Collections.sort(List)` 의 첫번째 인자로 `**Comparable` 인터페이스를 구현한 객체의 리스트**를 넘긴다.

> 예제
> 

```java
class Number implements Comparable<Number> {
    private final int value;

    Number(int value) {
        this.value = value;
    }

    @Override
    public int compareTo(Number other) {
        if (this.value > other.value) {
            return 1;
        }

        if (this.value < other.value) {
            return -1;
        }

        return 0;
    }
}
```

### 2. Comparator

> `Comparator` 는 타입이 같은 객체 두개를 매개변수로 전달받아 **우선순위를 비교할 수 있는 정렬자를 만들기 위해** 사용되는 인터페이스다.
> 
- `Comparator` 를 구현하기 위해서 `compare(T o1, T o2)` 메서드를 오버라이딩 해야한다.
- `Collections.sort(List, Comparator)` 의 두번째 인자로 `**Comparator` 구현체**를 넘긴다.
    - `Comparator` 구현체 대신 `람다`나 `익명 클래스`를 사용할 수 있다.

> 예제
> 

```java
class NumberComparator implements Comparator<Number> {
    @Override
    public int compare(Number number1, Number number2) {
        return number1.getValue() - number2.getValue();
    }
}
```

### 3. Comparator 용도

- 정렬 대상 클래스의 코드를 수정할 수 없을 때
- 정렬 대상 클래스에 이미 정의된 `compareTo()` 와 다른 기준으로 정렬하고 싶을 때
- 여러 정렬 기준을 적용하고 싶을 때

### Reference

- Comparable, Comparator
    - [[Java] Comparable 과 Comparator](https://hudi.blog/java-comparable-and-comparator/)
