# https://github.com/WeeklyStudy/modern-java-in-action/issues/7 메서드 참조를 사용하는 이유는 무엇이고 메서드 참조 유형에는 무엇이 있을까?
## 💡메서드 참조(Method Reference)란?

> 람다식이 단 하나의 메서드만 호출할 경우에 해당 람다식에서 불필요한 매개변수를 제거하고 사용하는 방법이다.
> 
- `이중 콜론(::)` 을 사용하여 클래스명과 메서드명을 구분한다.
- 람다식과 달리 인수를 전달할 필요가 없다.
- 실제로 메서드를 호출하는 것이 아니라 참조만 한다.

## 💡메서드 참조의 장점

1. **더 짧고 간결한 코드**를 만들 수 있다.
2. 명시적으로 메서드명을 참조하여 **코드의 가독성을 높일 수 있다.**

> [참고] 람다가 메서드 참조보다 간결한 경우
> 
> - 메서드와 람다가 같은 클래스에 있는 경우 람다가 더 간결한 코드를 작성할 수 있다.
>     
>     ```java
>     class Example {
>         public static void main(String[] args) {
>           // 메서드 참조
>     	    service.execute(GoshThisClassNameIsHumongous::action);
>     	    
>     	    // 람다
>     	    service.execute(() -> action());
>         }
>     }
>     ```
>     

## 💡메서드 참조의 유형

|  | 람다 | 메서드 참조 |
| --- | --- | --- |
| 정적 메서드 참조 | (x) -> ClassName.staticMethod(x) | ClassName::staticMethod |
| 한정적 인스턴스 메서드 참조 | (x) -> obj.instanceMethod(x) | obj::instanceMethod |
| 비한정적 인스턴스 메서드 참조 | (obj, x) -> obj.instanceMethod(x) | ClassName::instanceMethod |
| 클래스 생성자 | () -> new Object()
(x) -> new Object(x) | Object::new |
| 배열 생성자 | x -> new int[x] | int[]::new |

### 1. 정적 메서드 참조 → `클래스::정적 메서드`

가장 흔한 메서드 참조 유형으로 정적 메서드를 참조한다.

- 메서드 참조
    
    ```java
    Integer::parseInt
    ```
    
- 람다
    
    ```java
    str -> Integer.parseInt(str)
    ```
    

### 2. 한정적 인스턴스 메서드 참조 → `인스턴스::인스턴스 메서드`

이미 존재하는 인스턴스의 메서드를 참조한다.

- 메서드 참조
    
    ```java
    Instant.now()::isAfter
    ```
    
- 람다
    
    ```java
    Instant then = Instant.now();
    t -> then.isAfter(t)
    ```
    

### 3. 비한정적 인스턴스 메서드 참조 → `클래스::인스턴스 메서드`

함수 객체를 적용하는 시점에 수신 객체를 알려준다.

- 메서드 참조
    
    ```java
    String::toLowerCase
    ```
    
- 람다
    
    ```java
    str -> str.toLowerCase()
    ```
    

### 4. 클래스 생성자 → `클래스::new`

클래스 생성자를 가리키는 메서드 참조다.

- 메서드 참조
    
    ```java
    TreeMap<K,V>::new
    ```
    
- 람다
    
    ```java
    () -> new TreeMap<K, V>()
    ```
    

### 5. 배열 생성자

배열 생성자를 가리키는 메서드 참조다.

- 메서드 참조
    
    ```java
    int[]::new
    ```
    
- 람다
    
    ```java
    len -> new int[len]
    ```
    

## 💡결론

메서드 참조를 사용해 더 간결하고 명확한 의미를 나타낼 수 있다면 메서드 참조를 쓰고 그렇지 않다면 람다를 사용하자.

## Reference

- 메서드 참조란
    - [자바 1.8 - 메서드 참조란? 세미콜론 두개란? (::)](https://way-be-developer.tistory.com/184)
    - [[Java]메서드 참조(Method reference)](https://developer-talk.tistory.com/462)
- [이펙티브 자바] 메서드 참조 유형
    - [43_람다보다는_메서드_참조를_사용하라_김석래.md](https://github.com/Meet-Coder-Study/book-effective-java/blob/main/7%EC%9E%A5/43_%EB%9E%8C%EB%8B%A4%EB%B3%B4%EB%8B%A4%EB%8A%94_%EB%A9%94%EC%84%9C%EB%93%9C_%EC%B0%B8%EC%A1%B0%EB%A5%BC_%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC_%EA%B9%80%EC%84%9D%EB%9E%98.md)
    - [람다보다는 메서드 참조를 사용하라.md](https://github.com/woowacourse-study/2022-effective-java/blob/main/07%EC%9E%A5/%EC%95%84%EC%9D%B4%ED%85%9C_43/%EB%9E%8C%EB%8B%A4%EB%B3%B4%EB%8B%A4%EB%8A%94%20%EB%A9%94%EC%84%9C%EB%93%9C%20%EC%B0%B8%EC%A1%B0%EB%A5%BC%20%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC.md)
    - [[Effective Java] 아이템 43 : 람다보다는 메서드 참조를 사용하라](https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-43-%EB%9E%8C%EB%8B%A4%EB%B3%B4%EB%8B%A4%EB%8A%94-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%B0%B8%EC%A1%B0%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)
    - [[이펙티브 자바] Item43- 람다보다는 메서드 참조를 사용하라](https://jjingho.tistory.com/92)
- 인스턴스 메서드 참조
    - [[Item43] 메서드 참조를 써야하는가](https://ryumodrn.tistory.com/103)
    - [[Java8] Method Reference (메서드 참조) - 생성 방법](https://countryxide.tistory.com/127)
- 람다를 메서드 참조로 바꾸는 방법
    - [JAVA - 람다와 스트림(3)](https://velog.io/@ch4570/JAVA-%EB%9E%8C%EB%8B%A4%EC%99%80-%EC%8A%A4%ED%8A%B8%EB%A6%BC3)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/8 람다 표현식과 익명 클래스의 차이점과 장단점이 무엇인가?
## 💡익명 클래스와 람다식의 차이점

| 익명 클래스 | 람다식 |
| --- | --- |
| 이름이 없는 클래스다.(새로운 클래스 파일이 생성된다.) | 이름이 없는 메서드다. |
| this는 익명 클래스 인스턴스 자신을 가리킨다. | this는 람다가 선언된 외부 클래스 인스턴스를 가리킨다. |
| 추상 메서드가 여러 개인 인터페이스를 구현할 수 있다. | 추상 메서드가 1개인 함수형 인터페이스를 구현할 수 있다. |

## 💡람다식의 장점

1. 코드가 간결해진다.
2. 가독성이 높아진다.
3. 병렬 프로그래밍이 용이하다.

## 💡람다식의 단점

1. 람다를 사용해서 만든 익명함수는 재사용이 불가하다.
2. 디버깅이 어렵다.
    - 람다는 내부적으로 수행하는 작업이 더 많기 때문에 문제의 원인을 파악하기 어렵다.

## 💡결론

1. 람다는 1~3줄 내로 작성하자. 코드 자체가 이해하기 어렵고 길어지면 익명 클래스를 쓰자.
2. 람다는 함수형 인터페이스에서만 사용할 수 있다. 추상 클래스의 인스턴스나 추상 메서드가 여러 개인 인터페이스의 인스턴스를 만들 때는 익명 클래스를 사용해야 한다.
3. 람다는 자신을 참조할 수 없다. 따라서, 함수 객체가 자신을 참조해야 한다면 반드시 익명 클래스를 사용해야 한다.

## Reference

- 익명 클래스와 람다식의 차이점
    - [익명 클래스와 람다식의 차이](https://gimmesome.tistory.com/209)
    - [[Java]익명 내부 클래스와 람다식의 차이점](https://developer-talk.tistory.com/499)
    - [ITEM 42: 익명 클래스보다는 람다를 사용해라](https://dahye-jeong.gitbook.io/java/java/effective_java/2021-06-19-prefer-lambdas-to-anonymous-classes)
- 람다식의 장단점
    - [[Java] 람다식(Lambda Expression)과 함수형 인터페이스(Functional Interface) - (2/5)](https://mangkyu.tistory.com/113)
- 결론
    - [[이펙티브 자바] 아이템 42. 익명 클래스보다는 람다를 사용하라](https://velog.io/@injoon2019/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C-42.-%EC%9D%B5%EB%AA%85-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%B3%B4%EB%8B%A4%EB%8A%94-%EB%9E%8C%EB%8B%A4%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/9 람다캡처링이란 무엇인가?
## 💡람다 캡처링이란?

> 람다캡처링은 **람다 외부에서 정의된 변수를 람다식 내부에서 사용하는 것**이다.
> 
- 인스턴스 변수와 정적 변수는 자유롭게 람다에서 참조할 수 있다.
- 지역변수는 `final` 혹은 `effectively final(값이 재할당되지 않는)` 변수만 람다에서 사용할 수 있다.

## 💡람다에서 final 지역 변수만 사용 가능한 이유

람다 캡처링의 지역 변수 제약을 지키지 않는 경우 아래와 같은 오류가 발생한다.

> Variable used in lambda expression should be final or effectively final
> 

그렇다면, 왜 지역 변수 사용에만 제약이 생긴걸까?

결론부터 말하자면 **`인스턴스 변수` 는 `힙` 에 저장되고 `지역 변수` 는 `스택` 에 저장되기 때문**이다.

스택 영역은 각 스레드가 독립적으로 가지며 힙 영역은 스레드 간에 공유한다.

즉, 인스턴스 변수와 정적 변수는 스레드 간 공유를 하지만 **지역 변수는 스레드 간에 공유할 수 없다.**

따라서 람다 캡처링이 발생할 때 지역 변수의 복사본을 사용하는데, 복사본의 값을 바꾼다 해도 원본의 값은 변경할 수 없기 때문에 지역 변수에 값을 재할당하는 것을 허용하지 않는다.

> [참고] 람다 캡처링 과정
> 
> 1. 람다가 실행되면서 새로운 스택이 생성된다.
> 2. 실행되고 있던 기존 메서드의 스택 데이터 값들이 람다의 스택에 그대로 복사된다.

## 💡결론

- 람다식에서 `인스턴스 변수`와 `정적 변수`는 자유롭게 사용 가능하다.
- 람다식에서 사용하는 외부 `지역 변수`는 `복사본`이다.(call by value)
- 람다식에서 `기본형 변수`인 `지역 변수`의 `값`은 바꿀 수 없다.
- 람다식에서 `참조 변수`인 `지역 변수`의 `참조값`을 바꾸는 것은 불가능하지만 `참조하고 있는 데이터`를 변경하는 것은 가능하다.

## Reference

- 람다캡처링 개념
    - [[자바] 람다 캡처링](https://velog.io/@injoon2019/%EC%9E%90%EB%B0%94-%EB%9E%8C%EB%8B%A4-%EC%BA%A1%EC%B2%98%EB%A7%81)
- 람다 캡처링 제약 조건
    - [람다 캡처링 :: Variable used in lambda expression should be final or effectively final의 이유*](https://cobbybb.tistory.com/19)
    - [[Java] 람다 ( Lambda ) 와 캡쳐링](https://internet-craft.tistory.com/32)
