## 문제 1

람다식을 만든다는 것은 statement를 expression으로 바꾸는 것이다. 즉, 익명함수의 일종인 람다는 익명 클래스를 간결화한 버전이라고 생각된다.
따라서 람다를 적절한 케이스에 잘 활용하려면 두 방식의 차이를 깊게 이해하는 것부터 시작해야 한다고 생각한다.

람다 표현식과 익명 클래스의 차이점과 장단점이 무엇일까?

## 답변 1

### 1. 익명 클래스

코드 내부에 이름이 존재하지 않는 클래스를 만드는 것

- 장점:
    - 다양한 인터페이스 및 메서드 구현
        - 익명 클래스는 모든 인터페이스에 대해 구현체를 생성할 수 있다.
        - 또한, 부모 클래스를 상속하여 메서드 오버라이딩을 통해 동작을 재정의할 수 있다.
    - 명시적인 타입 선언
        - 익명 클래스는 명시적으로 타입을 선언함으로써 필요에 따라 가독성을 높일 수 있다.
- 단점:
    - 높은 메모리 사용량
        - 새로운 클래스가 생성되어 메모리 사용량이 증가할 수 있다.
    - 낮은 가독성
        - 코드가 길고 복잡해질 수 있어 가독성이 떨어질 수 있다.

### 2. 람다 표현식

익명 함수를 간결하게 표현하기 위한 문법적인 기능

- 장점:
    - 가독성과 간결성
    - 함수형 프로그래밍 지원
        - 람다 표현식은 함수형 프로그래밍 개념을 지원하며, 함수를 값으로 다룰 수 있는 장점이 있다.
        - 함수를 값으로 활용할 수 있다는 장점은 문제에서 언급한 람다의 정의(statement → expression으로의 변화)와 일맥상통한다.
    - 메모리 절약
        - 람다 표현식은 익명 클래스에 비해 더 가볍게 작동한다.
        - 클래스 내에 정의되어 있는 메서드에 접근하고 이를 참조하기 위해서는 객체 초기화가 이루어져야 한다.
        - 하지만, 람다는 기본적으로 함수형 인터페이스의 구현이므로, 불필요한 클래스 인스턴스 생성을 피할 수 있다. [지연 연산]
- 단점:
    - 제약 사항
        - 함수형 인터페이스(하나의 추상 메서드만을 정의하는 인터페이스)에만 사용 가능하다.
    - 디버깅
        - 디버깅이 어려울 수 있다.

### 3. 익명클래스와 람다 표현식 차이점

- **활용**에서의 차이
   - **익명 클래스**: `부모 클래스를 상속`하거나 `인터페이스를 구현`할 때 사용된다.
        - 사실 현업에서는 일반적인 클래스를 익명 클래스로 코드를 작성하는 경우는 거의 없고, 별도의 Java 파일을 통해 작성한다고 한다.
        - 익명 클래스 역시 람다와 마찬가지로 해당 스코프 내에서만 사용되기 때문에 부모 클래스를 상속하는 경우는 주로 추상 클래스를 상속하는 경우다.
    - **람다 표현식** : `함수형 인터페이스를 구현`할 때만 사용 가능하다.
- **클래스**와 **함수**로서의 차이
    - **익명 클래스**: 
        - `클래스`의 일부로서 멤버 변수와 메서드를 가질 수 있으며, 클래스 내부에서 상태를 유지할 수 있다. 
        - 따라서, 익명 클래스에서 this는 `인스턴스 자신`을 가리킨다.
    - **람다 표현식**: 
        - (멤버 변수를 포함하여) 외부 변수를 캡처하여 사용할 수 있지만, 상태를 유지하는 멤버 변수를 직접 선언할 수는 없다. 람다식은 익명`함수`로, 클래스가 아니기 때문이다. (We can only have members in the class, i.e. variables ( with or without initialization) and methods and initialization blocks.)
        - 이에 람다식에서 this는 자기 자신이 아닌 `람다식을 실행한 객체`(즉, 외부 인스턴스)를 가리킨다.

    ```java
   // this 차이에 관한 예제 코드

    import java.util.Arrays;
    import java.util.List;


    class Anonymous {
        public void say() {}
    }

    public class Main {
        public void someMethod() {
            List<Anonymous> list = Arrays.asList(new Anonymous());

            Anonymous anonymous = new Anonymous() {
                @Override
                public void say() {
                    System.out.println("this instanceof Anonymous : " + (this instanceof Anonymous));
                }
            };
            // 익명 클래스에서의 this
            // this instanceof Anonymous : true
            anonymous.say();

            // 람다에서의 this
            // this instanceof Main : true
            list.forEach(o -> System.out.println("this instanceof Main : " + (this instanceof Main)));
        }

        public static void main(String[] args) {
            new Main().someMethod();
        }
    }
    ```

#### 💡 멤버변수와 로컬변수
> - 멤버변수 : 클래스의 필드에 선언된 변수로, 클래스 변수와 인스턴스 변수가 이에 속한다.
>    - 클래스 변수 : 클래스 전체에서 공유되는 멤버 변수. static을 붙여 객체의 생성과는 관계없이 클래스가 로드될 때 초기화되며 유지되므로 정적변수라고 부르기도 한다.
>    - 인스턴스 변수 : 인스턴스 별로 독립적으로 유지되는 멤버 변수
> - 로컬변수 : 메소드가 실행될 때만 사용되는 변수로, 메소드 내부에 선언되어 있다.


### 4. 결론
- 익명 클래스와 람다 표현식은 모두 익명으로 정의되는 코드 블록이나 함수를 생성하는 방식으로, 일회성이 강하다.
- 어떤 방식을 선택할지는 상황과 요구 사항에 따라 다르지만, Java 8부터는 람다 표현식이 `함수형 프로그래밍`과 `간결성` 측면에서 더 선호되고 많이 사용되고 있다.
- 그러나, 함수형 프로그래밍을 지원하는 람다는 `함수형 인터페이스`에는 사용 가능하지만, 그 외 `추상클래스`나 `기타 다른 인터페이스`에서는 람다 사용이 불가능하기 때문에 익명 클래스를 사용해야 한다.
- 또한, 익명 클래스와 람다 표현식은 각각 `클래스`와 `함수`의 형태를 가지고 있으므로, 클래스와 함수로부터 유래한 특성들(멤버변수 선언 가능여부 등)을 구분하여 이해해야 한다.

### Reference

- [[Java] 익명클래스와 Lambda](https://velog.io/@jerry92/Java-%EC%9D%B5%EB%AA%85%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80-Lambda)
- [자바의 람다식(Lambda Expression), 그 존재의 이유에 대한 깊은 고찰 *](https://peterdrinker.tistory.com/383)
- [[심화] 익명 클래스와 람다 *](https://www.codelatte.io/courses/java_programming_basic/O2PZAC2T82LKBXAY)
- [[Java] 익명객체(익명클래스)란? *](https://limkydev.tistory.com/226)
- [[Java] 변수 구분하기 (클래스변수/ 인스턴스변수/ 지역변수)](https://jishushu.tistory.com/entry/%EB%B3%80%EC%88%98-%EA%B5%AC%EB%B6%84%ED%95%98%EA%B8%B0-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%B3%80%EC%88%98-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%EB%B3%80%EC%88%98-%EC%A7%80%EC%97%AD%EB%B3%80%EC%88%98)
- [[이펙티브 자바 3판] 아이템 42. 익명 클래스보다는 람다를 사용하라](https://madplay.github.io/post/prefer-lambdas-to-anonymous-classes)
- [[StackOverFlow] field declaration in anonymous class *](https://stackoverflow.com/questions/34333866/statements-in-anonymous-class-declaration)

## 문제 2

IntelliJ에서 가끔 람다식을 메서드 참조로 바꾸도록 제안하는 메시지(`replace lambda with method reference`)를 본적이 있다. 메서드 참조에 대해 잘 모르는 상태에서 추천해주는 대로 람다를 메서드 참조로 바꾼적이 몇 번 있었는데, 앞으로는 왜 사용하는지 정도는 알고 쓰는 것이 좋을 것 같다는 생각이 들었다.

따라서, **메서드 참조를 사용하는 이유(혹은 장점)과** **메서드 참조 유형**에 대해 알아보자.

## 답변 2

### 1. 메서드 참조(Method Reference)란?

람다가 메서드를 직접 호출하지 않고, 메서드명을 직접 참조하는 것이다.

### 2. 람다 대신 메서드 참조하는 이유?

특정 메서드를 호출하는 람다의 축약형으로,  불필요한 매개변수를 제거하여 간결하고 가독성 높은 코드를 구현할 수 있다.

### 3. 메서드 참조유형
|유형|문법|예시|
|---|---|---|
|정적 메서드 참조 | `ClassName::staticMethodName`|`Integer::parseInt`|
|선언된 객체의 인스턴스 메서드 참조 | `instance::instanceMethodName`|(Transaction 클래스 객체를 할당받은 expensiveTransaction 지역 변수가 있고 Transaction 객체에는 getValue 메서드가 있을 시) `expensiveTransaction::getValue`|
|객체의 인스턴스 메서드 지정 | `ClassName::instanceMethodName`|`String::length`|
|생성자 참조 | `ClassName::new` |`TreeMap::new`|


### Reference

- [람다의 메서드 참조](https://devfunny.tistory.com/340)
- [Java 메서드 참조란?](https://develop-writing.tistory.com/136)


## 문제 3

지역 변수 사용과 자유 변수에 관련하여 람다 캡처링이라는 용어가 등장하는데 람다 캡처링과 관련된 무엇인지에 대해 궁금증이 생겨 찾아보려고 한다.

람다캡처링이란 무엇인가?

## 답변 3

### 1. 람다 캡처링이란?

자유 변수의 복사본을 람다 바디에서 로컬 변수 값(value)에 접근을 허용하는 행위이다.

#### 💡 자유변수(free variable)란?
> 람다식 내에서 정의되지 않고, 또 파라미터로도 전달되지 않은 외부 local variable를 참조하는 변수이다.

### 2. 람다 캡처링의 제약 조건

람다는 인스턴스 변수와 정적 변수를 자유롭게 캡처해서 자신의 바디부분에서 참조할 수 있는데, 이 경우 지역 변수는 `effectivly final variable` 이어야 한다. 즉, 명시적으로 final 키워드가 붙거나 final처럼 변경없이 사용해야 한다는 것이다.

이는 지역 변수의 생성과 소멸이 스레드의 생성과 소멸과 함께 가기 위함이다.
가변적인 지역 변수를 캡처할 경우, 람다 표현식의 바디를 작성할 때 경합 상태(race condition)가 일어날 수 있기 때문이다.
따라서, 람다 표현식이 캡처된 스레드를 벗어나지 않도록 컴파일 타임에 검사하기 위해 강제적으로 동기(Sync)를 맞추는 것이다.

#### 💡 람다에서 인스턴스 변수에는 final 제약이 없는 이유
>  - 인스턴스 변수는 Heap 영역에 선언 되어 Thread 별로 모두 접근이 가능하기 때문에, 지역 변수와 달리 value 복사 과정이 불필요하고 참조시 최신 값임을 보장할 수 있다.
> - 즉, `변수 범위 관점`에서 살펴봤을 때 인스턴스 자체는 이미 전역적으로 공유되고 있기 때문에 final을 통해 동기화를 강제하지 않고 있는 것이다.

### Reference

- [람다 캡처링](https://catsbi.oopy.io/9b757e48-a756-4469-973e-a06d0f34e7a4)
- [[Java]-Lambda와 Closure의 관계](https://velog.io/@jylee9937/Java-Lambda%EC%99%80-Closure%EC%9D%98-%EA%B4%80%EA%B3%84)
- [[StackOverFlow] Why are only final variables accessible in anonymous class?](https://stackoverflow.com/questions/4732544/why-are-only-final-variables-accessible-in-anonymous-class)
- [[JAVA] lambda에서 지역변수가 fianl이어야 하는 이유](https://leekm0912.tistory.com/26)
- [자바 람다에서 final이거나 final처럼 쓰인 지역 변수만 접근할 수 있는 이유](https://jeong-pro.tistory.com/211)
- [[StackOverFlow] Lambdas: local variables need final, instance variables don't](https://stackoverflow.com/questions/25055392/lambdas-local-variables-need-final-instance-variables-dont)
