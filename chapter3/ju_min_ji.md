## Q1. 람다 표현식과 익명 클래스의 차이점과 장단점이 무엇일까?

### 1. 익명 클래스

클래스를 정의하면서 동시에 인스턴스를 생성하는 특별한 형태의 클래스로, 코드 내부에 존재하는 이름이 존재하지 않는 클래스다.

- 장점:
    - 다양한 인터페이스 및 메서드 구현
        - 익명 클래스는 모든 인터페이스에 대해 구현체를 생성할 수 있다.
        - 또한, 부모 클래스를 상속하여 메서드 오버라이딩을 통해 동작을 재정의할 수 있다.
    - 명시적인 타입 선언
        - 익명 클래스는 명시적으로 타입을 선언함으로써 필요에 따라 가독성을 높일 수 있다.
- 단점:
    - 낮은 가독성
        - 코드가 길고 복잡해질 수 있어 가독성이 떨어질 수 있다.

### 2. 람다 표현식

익명 함수를 간결하게 표현하기 위한 문법적인 기능으로, 익명 구현 객체(anonymous implementation object)이다.

- 장점:
    - 간결성
       - 람다 표현식은 주로 함수형 인터페이스와 함께 사용되는데, 이 경우 함수형 인터페이스를 구현하는 익명 클래스의 인스턴스를 생성하지 않고 람다 표현식으로 함수를 정의하여 사용가능하다.
       - 즉, 클래스 정의 및 인스턴스화하는 코드 작성이 불필요하다.
        ```java
        // 인터페이스를 구현하는 익명 클래스 인스턴스화
       RunSomething runSomething = new RunSomething() { 
            @Override
            public int doIt(int number) {
                return number + 5;
            }
        };
        
        //  인터페이스를 구현하는 람다 표현식
        RunSomething lambda = number -> number + 5; 
        ```       
    - 함수형 프로그래밍 지원
        - 람다 표현식은 함수형 프로그래밍 개념을 지원하며, 함수를 일급 객체로 취급하여 `값`으로 사용 할 수 있다.
           - 람다 표현식의 기본 문법은 expression style 문법이다. 
           - 그러나, statement도 return문을 이용하여 람다로 표현할 수 있는데, 이를 block style 문법이라고 한다.
- 단점:
    - 제약 사항
        - 함수형 인터페이스(하나의 추상 메서드만을 정의하는 인터페이스)에만 사용 가능하다.
#### 💡 함수 vs 메서드
> - 람다식은 메서드가 아닌 함수의 일종이다. 
> - 메서드는 클래스에 종속적인 것을 메서드라 하지만, 함수는 어느곳에 종속적이지 않다.
#### 💡 expression vs statement
> - expression은  ‘표현식’이라는 뜻으로 하나 이상의 값으로 표현(reduce)될 수 있는 코드를 의미한다.
> - 핵심은 expression들은 평가(evaluate)가 가능해서 하나의 ‘값’으로 환원되어 function에 argument로 넘길 수 있다.
> - 반면 statement는 '명령문' 이라는 뜻으로 실행가능한(executable) 최소의 독립적인 코드 조각을 의미한다.
> - statement도 expression처럼 값으로 넘기는 방법이 있다. return문을 이용하는 것이다.
> - 정리하자면, return문 없이도 function에 argument로 넘길 수 있는 것은 expression, return문을 통해 expression처럼 값을 넘길 수 있는 것은 statement다.

### 3. 익명클래스와 람다 표현식 차이점
- 동작 방식
    - **익명 클래스**: 익명 클래스는 `컴파일 타임`에 특정 클래스의 하위 클래스로 변환되며, 새로운 클래스 파일이 생성된다. 
    - **람다 표현식**: `컴파일 타임`에는 람다식 구성 방법(recipe)만 설명(describe)해두고, 실제 구성(construct)은 `런타임`에 위임한다. 람다식 구성 내용은 invokedynamic의 정적 및 동적 인수 목록에 인코딩된다.
- 활용
   - **익명 클래스**: `부모 클래스를 상속`하거나 `인터페이스를 구현`할 때 사용된다.
        - 현업에서는 일반적인 클래스를 익명 클래스로 코드를 작성하는 경우는 거의 없고, 주로 추상 클래스의 추상 메서드나 인터페이스를 구현할 때 사용되어 별도의 Java 파일을 통해 작성한다고 한다.
    - **람다 표현식** : `함수형 인터페이스의 구현`을 간결하게 나타내는데 사용된다. 
- 상태 유지
   - **익명 클래스**: 클래스의 정의와 함께 인스턴스를 생성하므로, `내부 멤버 변수`와 메서드를 가질 수 있다. 이를 통해 객체의 상태를 유지하고 메서드를 호출하여 다양한 동작을 수행할 수 있다.
   - **람다 표현식**: `외부 범위의 변수를 캡처`하여 사용할 수 있지만(람다 캡처링), 자체적인 상태를 유지하는 멤버 변수를 직접 선언하고 사용할 수는 없다.
- this 참조
    - **익명 클래스**: this는 `익명 내부 클래스 객체`를 가리킨다.
    - **람다 표현식**: this는 `람다식을 실행한 객체`(즉, 외부 인스턴스)를 가리킨다.
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
- Java 8 이전에도 존재하던 **익명 클래스**는 `인터페이스`나 `추상 클래스`의 구현체로 사용되며, 일회성 객체를 구현할 때 유용하다.
- 그러나, Java 8부터는 **람다 표현식**이 `함수형 프로그래밍`과 `간결성` 측면에서 더 선호되고 많이 사용되고 있다.
- **람다 표현식**은 `함수형 인터페이스`의 구현을 간결하게 나타내는데 사용되며, 외부 변수의 캡처와 함수 전달을 간편하게 처리한다. 이로 인해 함수를 매개변수로 전달하거나 함수를 반환하는 고차 함수의 `인자`로 사용될 때 유용하고, 스트림 API와 함께 사용하여 데이터 처리 `파이프라인`을 구성하는 데도 자주 활용된다.
- 정리하자면, 함수형 인터페이스에서는 익명 클래스 대신 간결하게 **람다**를 사용할 수 있지만, **익명 클래스** 자리를 람다가 완전히 대체할 수 있는 것은 아니다.

### Reference
- 익명 클래스와 람다
   - [[심화] 익명 클래스와 람다 *](https://www.codelatte.io/courses/java_programming_basic/O2PZAC2T82LKBXAY)
   - [람다식(feat. 익명 구현 클래스 vs 람다식) *](https://alkhwa-113.tistory.com/entry/%EB%9E%8C%EB%8B%A4%EC%8B%9Dfeat-%EC%9D%B5%EB%AA%85-%EA%B5%AC%ED%98%84-%ED%81%B4%EB%9E%98%EC%8A%A4-vs-%EB%9E%8C%EB%8B%A4%EC%8B%9D)
   - [자바의 람다식(Lambda Expression), 그 존재의 이유에 대한 깊은 고찰](https://peterdrinker.tistory.com/383)
   - [[Java] 익명객체(익명클래스)란?](https://limkydev.tistory.com/226)
   - [[이펙티브 자바 3판] 아이템 42. 익명 클래스보다는 람다를 사용하라](https://madplay.github.io/post/prefer-lambdas-to-anonymous-classes)
  - [Java 람다의 실체 - 람다 내부 동작 방식](https://ntalbs.github.io/2019/java-lambda/)
  - [람다식의 처리 과정](https://dreamchaser3.tistory.com/5)
  - [[StackOverFlow]How will Java lambda functions be compiled?](https://stackoverflow.com/questions/16827262/how-will-java-lambda-functions-be-compiled)
  - [[StackOverFlow]Is a class being instantiated in a lambda expression?](https://stackoverflow.com/questions/52873659/is-a-class-being-instantiated-in-a-lambda-expression)
  - [[StackOverFlow] field declaration in anonymous class](https://stackoverflow.com/questions/34333866/statements-in-anonymous-class-declaration)  
  - [[StackOverFlow] Can Java Lambdas have state?](https://stackoverflow.com/questions/48401216/can-java-lambdas-have-state)
  - [[StackOverFlow] Is lambda really an object or not?](https://stackoverflow.com/questions/70760212/is-lambda-really-an-object-or-not)
  - [[StackOverFlow] Java8 Lambdas vs Anonymous classes](https://stackoverflow.com/questions/22637900/java8-lambdas-vs-anonymous-classes)
- 부가적인 개념
   - [코드 단위인 Expression과 Statement의 차이를 알아보자](https://shoark7.github.io/programming/knowledge/expression-vs-statement)
  - [statement vs expression in lambda](https://ezsnote.tistory.com/entry/statements-vs-expressions-%EA%B5%AC%EB%AC%B8-vs-%ED%91%9C%ED%98%84%EC%8B%9D-in-lambda)
  - [[Java] 변수 구분하기 (클래스변수/ 인스턴스변수/ 지역변수)](https://jishushu.tistory.com/entry/%EB%B3%80%EC%88%98-%EA%B5%AC%EB%B6%84%ED%95%98%EA%B8%B0-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%B3%80%EC%88%98-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%EB%B3%80%EC%88%98-%EC%A7%80%EC%97%AD%EB%B3%80%EC%88%98)


## Q2. 메서드 참조를 사용하는 이유는 무엇이고 메서드 참조 유형에는 무엇이 있을까?

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


## Q3. 람다캡처링이란 무엇인가?

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
