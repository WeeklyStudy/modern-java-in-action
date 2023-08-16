# https://github.com/WeeklyStudy/modern-java-in-action/issues/26 Optional의 orElse() vs orElseGet()
    
## 💡orElse()

> 값이 있으면 값을 반환하고, 없으면 기본값을 반환한다.
> 
<img width="479" alt="스크린샷 2023-08-15 오후 4 42 30" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/ce0ac959-3b53-4a89-83d5-7b987c5c95b0">


- 파라미터로 `값` 을 받는다.

### 1. 값이 null이 아닌 경우

### 1.1. 예제 코드

```java
@Test
@DisplayName("[orElse] null이 아닌 경우")
void t1() {
    String userEmail = "user1@naver.com";
    String result = Optional.ofNullable(userEmail)
            .orElse(getUserEmail());

    System.out.println(result);
}
```

### 1.2. 실행 흐름

1. `Optional.ofNullable()` 이 호출되고 value가 `"user1@naver.com"` 인 `Optional` 객체 생성
2. `getUserEmail()` 이 호출되고 반환값을 `orElse()` 파라미터로 전달
3. `orElse()` 가 호출되고 value가 null이 아니므로 `"user1@naver.com"` 를 반환

### 1.3. 출력 결과

```
getUserEmail() Called
user1@naver.com
```

### 2. 값이 null인 경우

### 2.1. 예제 코드

```java
@Test
@DisplayName("[orElse] null인 경우")
void t2() {
    String userEmail = null;
    String result = Optional.ofNullable(userEmail)
            .orElse(getUserEmail());

    System.out.println(result);
}
```

### 2.2. 실행 흐름

1~2는 `1.2` 와 동일

1. `orElse()` 가 호출되고 value가 null이므로 `"user@naver.com"` 를 반환

### 2.3. 출력 결과

```
getUserEmail() Called
user@naver.com
```

## 💡orElseGet()

> 값이 있으면 값을 반환하고, 없으면 supplier의 결과값을 반환한다.
> 
<img width="414" alt="스크린샷 2023-08-15 오후 4 42 56" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/f68b895e-7516-4d1f-b15c-d9a2402dd077">


- 파라미터로 `함수형 인터페이스(Supplier)` 를 받는다.

### 1. 값이 null이 아닌 경우

### 1.1. 예제 코드

```java
@Test
@DisplayName("[orElseGet] null이 아닌 경우")
void t3() {
    String userEmail = "user1@naver.com";
    String result = Optional.ofNullable(userEmail)
            .orElseGet(this::getUserEmail);

    System.out.println(result);
}
```

### 1.2. 실행 흐름

1. `Optional.ofNullable()` 이 호출되고 value가 `"user1@naver.com"` 인 `Optional` 객체 생성
2. `getUserEmail` 함수 자체를 `orElseGet()` 파라미터로 전달
3. `orElse()` 가 호출되고 value가 null이 아니므로 `getUserEmail()` 이 실행되지 않고 `"user1@naver.com"` 를 반환

### 1.3. 출력 결과

```
user1@naver.com
```

### 2. 값이 null인 경우

### 2.1. 예제 코드

```java
@Test
@DisplayName("[orElseGet] null인 경우")
void t4() {
    String userEmail = null;
    String result = Optional.ofNullable(userEmail)
            .orElseGet(this::getUserEmail);

    System.out.println(result);
}
```

### 2.2. 실행 흐름

1~2는 `1.2` 동일

1. `orElse()` 가 호출되고 value가 null이므로 `getUserEmail()` 가 실행되어 `"user@naver.com"` 를 반환

### 2.3. 출력결과

```
getUserEmail() Called
user@naver.com
```

## 💡orElse로 인한 문제를 orElseGet으로 해결하기

`User` 의 `name` 이 unique 하다고 가정한다.

### 1. orElse로 인해 장애가 발생할 수 있는 예제

DB에 해당 `name`을 가진 `User`가 있든 없든 `createUserWithName()` 이 무조건 호출되기 때문에 중복된 `name` 인 경우 DB에 save 할 때 오류가 발생한다.

```java
public User findByName(String name) {
		return userRepository.findByName(name)
                            .orElse(createUserWithName(name));
}

private User createUserWithName(String name) {
		User user = new User(name);
		return userRepository.save(user);
}
```

### 2. orElseGet으로 해결

DB에 해당 `name`을 가진 `User`가 있는 경우에는 조회된 `User` 객체를 반환하고 없는 경우에는  `createUserWithName()` 가 호출되어 새로 생성된 `User` 객체를 반환한다.

```java
public User findByName(String name) {
		return userRepository.findByName(name)
                            .orElseGet(() -> createUserWithName(name));
}

private User createUserWithName(String name) {
		User user = new User(name);
		return userRepository.save(user);
}
```

## 💡결론

- `orElse` 는 파라미터로 `값` 을 받고 `orElseGet` 은 `함수형 인터페이스(Supplier)` 를 받는다.
- `orElse` 와 `orElseGet` 의 가장 큰 차이점은 **빈 Optional 에 대한 기본값을 언제 계산하는지**이다.
- `orElse(…)` 에서 `…` 은 Optional의 value가 null이든 아니든 무조건 실행되기 때문에 `값이 미리 존재하는 경우` 즉, `이미 생성되었거나 계산된 값일 때` 만 사용한다.
- `orElseGet(…)` 에서 `…` 은 Optional의 value가 null일 때만 실행되므로 `값이 미리 존재하지 않는 경우` 즉, `새로운 객체를 생성하거나 새로운 연산을 수행하는 경우` 에 사용한다.

## Reference

- [[Java] Optional이란? Optional 개념 및 사용법 - (1/2)*](https://mangkyu.tistory.com/70)
- [java, optional의 orElse와 orElseGet의 차이](https://cfdf.tistory.com/34)
- [Java Optional 바르게 쓰기*](https://homoefficio.github.io/2019/10/03/Java-Optional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%93%B0%EA%B8%B0/)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/27 자바의 직렬화(Serialization)와 Serializable 인터페이스는 무엇일까?
    
## 💡자바의 직렬화(Serialization)

> 자바 언어에서 사용되는 객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 바이트 형태로 데이터를 변환하는 기술과 바이트로 변환된 데이터를 다시 객체로 변환하는 기술(역직렬화)를 아울러서 이야기한다.
> 

### 1. 직렬화 조건

1. `기본형 타입`(int, char, String, short, double, long, byte 등)은 직렬화가 가능하다.
2. `Serializable` 인터페이스를 구현한 객체는 직렬화가 가능하다.

## 💡Serializable 인터페이스

- `Serializable` 인터페이스에는 아무런 메서드가 없다. 즉, 구현해야할 메서드가 없다.
- `Serializable` 인터페이스를 구현한 구현체가 직렬화 대상임을 JVM에게 알려주는 역할만 한다.
- 객체를 직렬화하기 위해 `Serializable` 인터페이스를 implements 해야한다. 그렇지 않으면 `NotSerializableException` 예외가 발생한다.
- `Serializable` 인터페이스를 구현한 구현체에서 `serialVersionUID` 값을 명시적으로 선언해주는 것을 권장한다. (만약 명시적으로 선언하지 않으면 컴파일러가 자동 생성한다.)
    
    ```java
    static final long serialVersionUID = 1L;
    ```
    

> 📍**serialVersionUID(SUID)**
> 
> - 직렬화, 역직렬화 과정에서 사용되는 클래스의 고유 식별 번호를 의미한다.
> - 클래스 직렬화, 역직렬화 과정에서 동일한 특성을 갖는지 확인하는데 사용된다.

## 💡Optional의 직렬화

- `Optional` 은 필드에 사용할 목적으로 만들지 않았기 때문에 `Serializable` 인터페이스를 구현하지 않았다. 그래서 `Optional` 을 필드로 갖는 클래스는 직렬화할 수 없는 문제가 생긴다.
- 직렬화가 필요하다면 필드를 `Optional` 로 선언하지 않고 대신 `Optional` 로 값을 반환받는 메서드를 추가하는 방식을 권장한다.

## Reference

- [자바 직렬화(Serializable) - 완벽 마스터하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A7%81%EB%A0%AC%ED%99%94Serializable-%EC%99%84%EB%B2%BD-%EB%A7%88%EC%8A%A4%ED%84%B0%ED%95%98%EA%B8%B0#%EC%9E%90%EB%B0%94%EC%9D%98_%EC%A7%81%EB%A0%AC%ED%99%94__%EC%97%AD%EC%A7%81%EB%A0%AC%ED%99%94)
- [[Java] 직렬화(Serialization)란 무엇일까?*](https://devlog-wjdrbs96.tistory.com/268)
- [JAVA 직렬화(Serializable)란?](https://haranglog.tistory.com/4)
- [자바 직렬화, 그것이 알고싶다. 훑어보기편](https://techblog.woowahan.com/2550/)
- [자바 객체의 직렬화(Serializable, serialVersionUID)*](https://ktko.tistory.com/entry/JAVA-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EC%A7%81%EB%A0%AC%ED%99%94Serializable-serialVersionUID)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/28 Optional의 장단점은 무엇일까?

## 💡자바 8 이전

### 1. 메서드가 특정 조건에서 값을 반환할 수 없을 때

자바 8 이전에는 메서드가 특정 조건에서 값을 반환할 수 없을 때 2가지 선택지가 있었다.

1. exception throw(예외 던지기)
    - 예외는 반드시 예외적인 상황에서만 사용해야 한다.
    - 예외는 실행 스택을 추적 캡처하기 때문에 비용이 비싸다.
2. null return(널 반환)
    - null을 반환하라 수 있는 메서드를 사용하는 곳에서 null 체크 로직을 추가해야 한다.

## 💡자바 8 이후

### 1. 메서드가 특정 조건에서 값을 반환할 수 없을 때

자바 8 이후에 `Optional<T>` 이 등장하면서 선택지 1개가 추가되었다.

3. 빈 Optional 반환
    - null 대신 `Optional.empty()` 를 리턴한다.

### 2. Optional

> null이 아닌 T타입 참조를 하나 담거나 아무것도 담지 않은 일종의 Wrapper 클래스다.
> 
- `Optional` 은 원소를 최대 1개 가질 수 있는 `불변 Collection`이다.
- `Optional을 반환하는 메서드`는 `예외를 던지는 메서드`보다 유연하고 사용하기 쉽다.
- `Optional을 반환하는 메서드`는 `null을 반환하는 메서드`보다 오류 가능성이 작다.
    
## 💡Optional의 장단점

### 1. 장점

1. 명시적으로 변수에 대한 null 가능성을 표현할 수 있다.
2. null일 수 있는 값을 직접 다룰 필요가 없으므로 Null Pointer Exception을 방지할 수 있다.
3. null 체크를 직접하지 않아도 되기 때문에 비즈니스 로직을 더 쉽게 파악할 수 있다.

### 2. 단점

1. Wrapper 클래스이기 때문에 생성 비용이 비싸다.
2. 직렬화가 불가능하기 때문에 클래스의 인스턴스 필드로 사용하면 안된다.

## 💡결론

- 결과 없음을 표현하기 위해 `Optional` 변수에 null을 할당하는 것 대신 `Optional.empty()` 를 사용하자.
- `Optional` 은 `반환 타입` 으로만 사용하자.
    - `Optional` 을 `인스턴스 필드`로 사용하면 메모리 사용량이 증가하고 직렬화 문제가 발생할 수 있어 권장하지 않는다.
    - `Optional` 을  `메서드/생성자 인자`로 사용하면 호출할 때마다 `Optional` 을 생성해서 전달해야 한다. 따라서 메서드에서 인자의 null 체크를 하자.
- 단순히 값을 얻을 목적이라면 `Optional` 을 생성해서 사용하는 것보다 `null` 비교를 하자.
- `컬렉션, 배열, 스트림` 은 빈 상태를 가질 수 있으므로 `Optional` 로 감쌀 필요가 없다.
- `Optional.of()` 는 null이 아님이 확실할 때만, `Optional.ofNullable()` 은 null일 수도 있을 때만 사용하자.
- `ifPresent()-get()` 대신 `orElse()/orElseGet()/orElseThrow()` 를 사용하자.
- 원시 타입의 값을 사용할 때는 `Optional<T>` 대신 `OptionalInt`, `OptionalLong`, `OptionalDouble` 을 사용하자.
- `Optional`의 내부 값을 비교할 때는 `get()` 으로 값을 가져올 필요없이 `Optional`의 `equals()` 로 비교하자.

## Reference

- [Java Optional을 이해하고 활용하자](https://kangmoo.github.io/posts/Java-Optional/)
- [Optional 과 null 에 대해*](https://tecoble.techcourse.co.kr/post/2021-06-20-optional-vs-null/)
- [[Java] JAVA8 Optional Class에 대한 정의 및 사용방법 - Tutorial(Sample)](https://maivve.tistory.com/332)
- [Java Optional 바르게 쓰기*](https://homoefficio.github.io/2019/10/03/Java-Optional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%93%B0%EA%B8%B0/)
- [Java 8 Null과 Optional - 2*](https://irerin07.tistory.com/108)
