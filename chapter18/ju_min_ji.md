## Q1. **불변 객체란?**

### 1. **Immutable(불변)이란?**

- 자바의 객체 타입에는 immutable(불변) 타입과 mutable(가변) 타입이 있다.
- 불변 객체는 생성 후 그 상태를 바꿀 수 없는 객체를 의미한다.
- 단순하게 생각하면 final 키워드처럼 초기화 이후에 값을 변경할 수 없다고 생각할 수 있다.
- **하지만 불변 객체는 값 변경이 아예 불가능한 것이 아니라, 힙 영역에 저장된 값을 수정할 수 없는 것이다.**
- 오직 새 객체를 만들어 래퍼런스 값을 주는 **재할당**만이 가능하고, 한 번 생성이 되면 신뢰할 수 있는 객체가 된다.
    - new 연산자로 객체를 생성하면 heap 영역에 객체가 생기고, 래퍼런스 값을 가지는 변수가 stack에 생길 것이다.
    - 그러나 재할당을 하면, 레퍼런스 변수가 새로운 객체를 가리키고, 기존 객체는 GC 대상이 된다.
- 재할당 예제 코드 - String Literal
    
    ```java
    String str = "first";
    str = "second"; // 재할당
    ```
    
- 불변 객체의 종류
    - String
    - Boolean, Integer, Float, Long 등의 기본 자료형 래퍼 클래스
    - BigInteger, BigDecimal
    - LocalDate, LocalTime, LocalDateTime 등의 java.time 패키지의 날짜 및 시간 관련 클래스
    - unmodifiableList, unmodifiableMap 등의 java.util 패키지의 불변 컬렉션 클래스

<br>

> ##### 💡 String Literal 재사용
> 
> 
> ```java
> String literal1 = "value";
>         String literal2 = "value";
>         String obj1 = new String("value");
>         String obj2 = new String("value");
> 
>         System.out.println(literal1.equals(literal2)); // true // 문자열 값 일치
>         System.out.println(literal1 == literal2); // true // 주소값 일치
>         
>         System.out.println(obj1.equals(obj2)); // true // 문자열 값 일치
>         System.out.println(obj1 == obj2); // false // 주소값 불일치
> ```
> 
> - Java에서 String은 특이하게 Heap 영역에서 String Constant Pool이 존재하는데, String을 Literal로 생성하면 이 영역에 저장되어 재사용된다.
> - 하지만 `new` 생성자로 생성하면 Constant Pool을 사용하지 않고, 일반적인 Heap 영역에 생성하여 재사용하지 않는다.
> - 따라서, 불변성의 장점을 위해 String Literal을 이용해서 String을 생성하는 것이 권장된다.

<br>

### 2. 불변 클래스로 설계하는 이유

- Thread-Safe
    - 불변객체는 근본적으로 쓰레드에 안전하여 동기화를 시킬 필요가 없다.
    - 즉, 불변 객체는 synchronized를 하지 않아도 동기화가 되기 때문에 멀티스레드 성능을 향상시키고, 안심하고 공유할 수 있게 한다.
- Failure Atomicity
    - 불변객체는 그 자체로 실패 원자성(호출된 메소드가 실패하더라도 해당 객체는 메소드 호출 전 상태가 유지되어야 한다)을 제공한다.
- 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많다.
    - 값이 바뀌지 않는 구성요소들로 이뤄진 객체라면 불변식을 유지하기에 수월하다.
    - 예를 들어 불변 객체는 맵의 키와 Set의 원소로 쓰기에 좋다.
- 불변 객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있다.
    
    ```java
    String str1 = "Hello"; // 불변 문자열 생성
    String str2 = "Hello"; // 같은 문자열을 다시 참조
    
    System.out.println(str1 == str2); // true, 같은 문자열을 공유함
    ```
    
    - 따라서, 자주 사용되는 값을 상수 혹은 불변 타입 클래스로 정의하여 재사용하면 메모리를 절약할 수 있다.
    - 또한, 불변 클래스를 생성하는 정적 팩토리 메서드를 통해 자주 사용되는 인스턴스를 **캐싱**하여 같은 인스턴스를 중복 생성하지 않게 한다.
        - Wrapper Class, BigInteger가 이에 해당한다.
        
        ```java
        // Integer.valueOf()는 Integer 객체를 생성하는 정적 팩토리 메서드이다.
        public static Integer valueOf(int i) {
             if (i >= IntegerCache.low && i <= IntegerCache.high)
                 return IntegerCache.cache[i + (-IntegerCache.low)];
             return new Integer(i);
         }
        ```
        

<br>

> ##### 💡 불변 클래스 고려 사항 - 인스턴스 생성 비용
> 객체를 다시 생성한다는 개념은 JavaBeans에서 생성된 개념에 대해 비효율적인 것처럼 보일 수 있다. 그러나 JDK의 거의 모든 release는 성능을 향상시키고, JDK를 발전시키는 엔지니어들은 빠른 객체 생성과 가비지 수집에 특히 열심히 노력한다. 그 결과, 메서드 호출 내에서만 존재하는 등의 수명이 짧은 데이터 객체의 생성과 재생성은 요즘 매우 빠르다.
> (출처: [javamagazine by Ian Darwin](https://blogs.oracle.com/javamagazine/post/java-immutable-objects-strings-date-time-records))

<br>

### 3. 불변 클래스(객체)를 만드는 방법

1. 상태를 변경하는 메서드 (setter 메서드 등) 을 제공하지 않는다.
2. Class 가 확장하지 않도록 한다 (final class)
3. 모든 필드에 final으로 선언한다.
4. 모든 필드를 private로 선언한다.
5. 자신을 제외하고는 가변 컴포넌트에 접근 불가하도록 만든다.
    - final을 선언하고 setter를 없애도 참조되는 객체가 불변객체가 아니라면 접근하여 값 조작이 가능하다. ([예제코드](https://yeoonjae.tistory.com/entry/Java-%EB%B6%88%EB%B3%80%EA%B0%9D%EC%B2%B4Immutable-Object%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90))
    - 따라서 불변객체가 되려면 **불변 객체의 내부 필드(참조 객체) 또한 불변으로 선언**하거나 **방어적 복사**(객체의 주소를 복사하지 않고 객체의 내부 값을 참조하여 복사하는 방법)를 이용해 참조관계를 끊어주어야 한다.

- 불변 클래스 예제 코드

  ```java
  @Entity
  @Setter  
  @Getter
  public class Member { // Entity
      @Id
      private Long id;
      private String name;
      private int age;
  }
  @Getter
  public class MemberDto { // DTO 역할을 하는 VO (DTO와 VO의 개념은 엄연히 다르나, 실무에선 혼용해서 사용한지 오래라 바꾸는 게 쉽지 않다고도 한다..)
		  private final String name;
      private final int age;
      private MemberDto(String name, int age) {
          this.name = name;
          this.age = age;
      }
      public static final MemberDto from (Member m) {
    	  return new MemberDto(m.getName(), m.getAge());
      }
  }
  ```

  - RESTful API 개발할 때 요청하고 응답하는 객체를 Entity, VO나 DTO 객체 등으로 분리하여 사용하곤 하는데, 실무에서는 이와 같이 불변 클래스를 이용하여 불변성을 강제하기도 한다.
      - name과 age 필드는 final로 선언되어 수정할 수 없다.
      - MemberDto 클래스는 생성자를 통해서만 객체를 생성하며, 생성자에서 초기화된 값은 이후 변경할 수 없다.
      - 외부에서 Member를 통해 MemberDto를 생성할 때에는 정적 팩토리 메서드인 from 메서드를 통해 Member 객체의 데이터를 복사하여 MemberDto 객체를 생성한다.
          - 이는 MemberDto 객체와 Member 객체 간에 데이터 공유를 방지하고 MemberDto 객체가 Member 객체의 상태에 영향을 미치지 않도록 한다.

- 불변 클래스 예시 - BigInteger

  - BigInteger 클래스는 불변 클래스이지만 Integer 클래스에서 설명했던 내용과 다른 부분이 존재한다.
  - 첫 번째로, 일부 필드를 private로 선언하지 않고 package-private로 선언하였다는 점이다.
  - 사실 위에서 설명한 원칙들은 다소 과하기 때문에 필요하다면 일부 완화할 수 있다. package-private도 높은 수준의 접근 제어자이기 때문에 어느 정도 수용 가능하다.
  - 하지만 private로 선언하지 않은 것과는 다르게 BigInteger를 개발할 때 큰 실수를 하였는데, 바로 상속을 못하도록 방어하지 않았다. 불행히도 하위 호환성 때문에 이는 고쳐지지 않고 사용되고 있다.
  - 그러나 나머지 구현 부분은 불변 클래스의 규칙을 따른다.

### 4. 결론

- 불변 객체는 Thread safe 하고, 예외가 발생해도 유효한 상태를 유지하며, 값이 다르다면 무조건 독립적인 객체로 생성된다.
- 책 Effective Java에서는 “*클래스들은 가변적이여야 하는 매우 타당한 이유가 있지 않는 한 반드시 불변으로 만들어야 한다. 만약 클래스를 불변으로 만드는 것이 불가능하다면, 가능한 변경 가능성을 최소화하라.”* 라고 말한다.
- 객체가 가질 수 있는 상태의 개수가 줄어드는 것은 그 객체를 예측하기가 쉬워지고 오류가 발생할 가능성도 줄어들게 되기 때문이다.

### References

- [Why is Java making so many things immutable? - Oracle Blogs](https://blogs.oracle.com/javamagazine/post/java-immutable-objects-strings-date-time-records)
- [[Effective Java] 아이템 17 - 변경 가능성을 최소화하라](https://ttl-blog.tistory.com/1205)
- [[이펙티브 자바 3판] 아이템 17. 변경 가능성을 최소화하라](https://madplay.github.io/post/minimize-mutability)
- [[이펙티브 자바] 규칙15. Immutable(불변) 클래스](https://camel-context.tistory.com/70)
- [자바의 String 객체와 String 리터럴](https://madplay.github.io/post/java-string-literal-vs-string-object)
- [Java - 불변객체(Immutable Object)에 대해](https://min103ju.github.io/effective%20java/immutable/)
- [[Java] Mutable과 Immutable - 최블랙의 개발로그](https://choiblack.tistory.com/47)
- [[Java] Immutable, Synchronized](https://wookcode.tistory.com/194)
- [[Effective Java] 불변 클래스 (Immutable class)](https://devkingdom.tistory.com/370)
- [[Java] 불변객체(Immutable Object)에 대해 알아보자](https://yeoonjae.tistory.com/entry/Java-%EB%B6%88%EB%B3%80%EA%B0%9D%EC%B2%B4Immutable-Object%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)


## Q2. **반복문과 재귀 방식의 차이점은 무엇이고 꼬리 재귀란 무엇인가?**

### 1. 반복문과 재귀의 차이점

|  | 반복문 | 재귀함수 |
| --- | --- | --- |
| 정의 | 명령을 반복적으로 실행 | 함수를 다시 호출하여 반복작업 수행 |
| 메모리 | 메모리 Heap 사용 | 함수 호출 시 함수의 매개변수, 지역변수, 리턴값, 함수 종료 후 돌아가는 위치가 Stack 메모리에 저장 |
| 무한 반복 시 | 무한 루프는 CPU 사이클을 반복적으로 사용 | 무한 재귀는 스택 오버플로우 발생 |
| 속도 | 빠른 실행 | 느린 실행 |

> ##### 💡 StackOverFlow
> 재귀함수 사용 시, 함수를 반복적으로 호출하므로, 스택메모리에 콜 스택이 쌓이게 된다. 이때 함수를 호출하는 횟수가 많아진다면 스택메모리를 초과하여 stack overflow가 발생하는 것이다.

### 2. 반복문보다 재귀를 사용하기에 적합한 경우

- 변화가 일어나지 않아야 하는 경우
    - 반복문에서는 변화가 자연스럽게 코드에 스며들 수 있지만, 재귀를 이용하면 변화가 일어나지 않는다.
    - 재귀 함수는 함수를 호출할 때마다 새로운 스택 프레임을 생성하여 해당 함수의 로컬 변수와 매개변수가 저장하기 때문에 함수 간의 상태 공유가 최소화되고, 오류를 발생시킬 수 있는 상황이 줄어든다.
    - 또한, 변수 사용 최소화를 통해 변수의 개수, 가질 수 있는 값의 종류 또는 범위를 제한하여 **mutable state(변경 가능한 상태)** 를 최대한 피할 수 있다.
- 알고리즘 자체가 재귀적인 표현이 자연스러운 경우
    - 이런 경우 재귀적인 표현을 통해 코드를 단순화하여 가독성을 높일 수 있다.

### 3. 꼬리 재귀

- 재귀가 가져다주는 장점에도 불구하고, 재귀는 메모리 사용량을 증가시켜 결국엔 StackOverFlowError을 발생시킬 수도 있다는 치명적인 단점이 있다.
- 하지만, 이는 **꼬리 호출 최적화(tail-call optimization)** 를 통해 해결할 수 있다.
- 이는 꼬리 재귀 호출이 끝난 후 현재 함수에서 추가 연산을 요구하지 않도록 구현하는 재귀의 형태이다.
- 꼬리 호출 최적화를 이용하면 함수 호출이 반복되어 스택이 깊어지는 문제를 컴파일러가 **선형으로** 처리하도록 알고리즘을 바꿔 스택을 재사용할 수 있게 된다.
    - 더이상 값이 변할 여지가 없으므로 스택을 덮어쓸 수 있기 때문이다.
- 하지만 이러한 꼬리 재귀를 사용하기 위해서는 컴파일러가 이런 최적화 기능을 지원하는지 먼저 확인해야 하는데, 스칼라, 그루비 같은 최신 JVM 언어는 이와 같은 재귀를 반복으로 변환하는 최적화를 제공하지만 아쉽게도 자바는 지원하지 않고 있다.
- 예제 코드
    - 일반적인 재귀함수
        
        ```c
        function factorial(n) {
        if (n === 1) return 1;
        return n * factorial(n -1);
        ```
        
    - 꼬리 재귀함수
        
        ```c
        functionfactorialTail(n, acc) {
          if (n === 1) return acc;
          return factorialTail(n - 1, acc * n);
        // 일반 재귀에서의 n * factorial(n - 1)과 달리 반환값에서 추가연산이 필요없다.
        }functionfactorial(n){
          return factorialTail(n, 1);
        }
        ```
        

### References

- [재귀함수와 반복문의 차이](https://hazel-developer.tistory.com/173)
- [[Algorithm] 반복문(iteration) vs 재귀(recursion)](https://yeonjewon.tistory.com/80)
- [Tail Recursion 꼬리 재귀](https://medium.com/@soyoung823/tail-recursion-%EA%BC%AC%EB%A6%AC-%EC%9E%AC%EA%B7%80-a84c2cd9a7e8)

## Q3. **함수형 프로그래밍에서 부작용(side effect)와 참조 투명성은 무엇인가?**

### 1. 부작용(side-effect)

- ISO/IEC 14882는 side effect라는 용어를 다음과 같이 정의하고 있다.
    
    ```
    Accessing an object designated by a volatile lvalue, modifying an object, calling a library I/O function, or calling a function that does any of those operations are all side effects, which are changes in the state of the execution environment.
    ```
    
- 즉, 프로그래밍에서 부작용이란 **실행 중에 어떤 객체를 접근해서** **변화가 일어나는** 아래와 같은 행위들을 의미한다.
    - 자료구조를 고치거나 필드에 값을 할당 및 변경(setter 메서드 같은 생성자 이외의 초기화 동작)
    - 예외 발생
    - 파일에 쓰기 등의 I/O 동작 수행
- 부작용 없는 시스템 컴포넌트에서는 메서드가 서로 간섭하는 일이 없으므로, **Lock을 사용하지 않고도 멀티코어 병렬성을 사용할 수 있다.**

### 2. Java 함수형 프로그래밍에서의 부작용

- 실질적으로 자바로는 완벽한 순수 함수형 프로그래밍을 구현하기 어렵다. 예를 들어 자바의 I/O 모델 자체에는 다음과 같은 부작용 메서드가 포함된다.
    - Scanner.nextLine()을 호출하면 파일의 행을 소비한다.
    - 즉, Scanner.nextLine()을 두 번 호출하면 다른 결과가 반환될 가능성이 있다.
- 따라서, 자바에서는 순수 함수형이 아니라 **함수형 프로그래밍**을 구현할 것이다. **실제 부작용이 있어도 아무도 이를 보지 못하게 함으로써 함수형을 달성할 수 있는 것이다.**
    - 예를 들어, 부작용이 없어야 하는 함수형 프로그래밍에서는 함수나 메서드가 어떤 예외도 일으키지 않아야 하지만, 비정상적인 경우에는 예외를 일으키는 것이 자연스러운 방식이다.
    - 치명적인 에러가 있을 때 처리되지 않은 예외를 발생시키는 것은 괜찮지만 예외를 처리하는 과정에서 함수형에 위배되는 제어 흐름은 ‘함수형’의 의의를 훼손시킨다고 보기도 한다.
    - 따라서 FP 개념이 강조된 자바8 이후에는 Optional을 통해 예외를 사용하지 않고도 결과값으로 연산을 성공적으로 수행했는지 못했는지를 확인할 수 있다.

### 3. 참조투명성
- 부작용을 감춰야 한다는 것은 ‘같은 인수로 함수를 호출했을 때 항상 같은 결과를 반환해야 한다’는 참조투명성 개념으로 귀결된다.
- 즉, 어떠한 표현식이 참조 투명하려면 그 표현식에서 순수 함수만 사용할 때, 참조 투명할 수 있기 때문에 함수가 자신의 결과를 변화시키는 전역 상태를 변경하면 안된다는 것을 뜻하기도 한다.
- 그렇다면 불변 객체만 참조 투명성을 준수할 수 있는가? 그건 아니다.
    - 가변 객체라도 순수한 값(Immutable Value)으로 취급한다면 참조적으로 투명하다고 볼 수 있다.
    - **다시 말해 객체의 내부 상태를 외부에서 변경하지 않는다면 가변 객체도 참조 투명성을 준수할 수 있다.**
- 이렇게 참조 투명성이 유지되면 추가 오버헤드를 피할 수 있는 **기억화**(memorization)이라는 기법을 사용할 수 있다.
    - 기억화는 메서드에 래퍼로 캐시(HashMap 같은)를 추가해, 래퍼가 호출되면 인수, 결과 쌍이 캐시에 존재하는지 먼저 확인 후 존재한다면 저장된 캐시를 가져오는 기법이다.
    - 하지만 해당 캐시 자료구조(HashMap 같은)는 다른 기억화에서도 공유되야 하므로 가변 상태이고 동기화되지 않았으므로 스레드 안정성이 없는 코드이다.
    - 따라서, **가장 좋은 방법은 함수형 프로그래밍을 사용해서 동시성과 가변 상태가 만나는 상황을 완전히 없애는 것이다.**
    - 캐싱을 구현할 것인지 여부와는 별개로 코드를 함수형으로 구현했다면 우리가 호출하려는 메서드가 공유된 가변 상태를 포함하지 않음을 미리 알 수 있으므로 동기화 등 신경 쓸 필요가 없어지기 때문이다.

### 4. 결론

- 함수형 프로그래밍에서는 가능한한 side effect를 최소화하고 참조 투명성을 유지하여 코드의 예측 가능성과 안정성을 높이는 것을 목표로 한다. 이를 통해 코드를 더 안정적이고 병렬 처리에 적합하게 만들 수 있다.
- 완벽한 순수 함수형은 현실적으로 어렵기 때문에, **내부적으로는** **함수형이 아닌 기능도 시스템의 다른 부분에 영향을 미치지 않는다면 함수형 프로그래밍**의 방식이다.
- Side effect를 다루는 방법에는 정답이 없지만, 적당한 수준의 side effect를 구현하는 것도 중요하다.
- 메서드명, 주석 또는 조직 내의 개발 규약을 통해 **메서드 내의 side effect가 명확히 드러나도록 하고, 그것을 인지하고 사용할 수 있도록 하는 것**이 side effect 사용의 이점을 극대화하고 단점을 최소화하는 데 도움이 될 것이다.

### References

- [프로그래밍에서 SIDE EFFECT 란? - hyeonk lab - 티스토리](https://hyeonk-lab.tistory.com/43)
- [CQS에 관한 고찰 (feat. Side Effect, 참조 투명성 그리고 순수 함수)](https://yoonpunk.tistory.com/19)
- [함수형 프로그래밍이란 무엇인가?](https://devbksheen.tistory.com/entry/%EB%AA%A8%EB%8D%98-%EC%9E%90%EB%B0%94-%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
