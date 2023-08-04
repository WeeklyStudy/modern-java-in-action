## Q1. 정적 **팩토리 메서드란 무엇인가?**

### 1.  정적 팩토리 메서드 패턴은 팩토리 메서드 패턴과는 다르다.

- 정적 팩토리 메서드 패턴과 팩토리 메서드 패턴은 **팩토리 패턴(객체 생성 역할을 별도의 클래스(Factory)에 위임하는 패턴)의 변형**이다.
- 하지만, 정적 팩토리 메서드는 [(책) GoF의 디자인 패턴](https://johngrib.github.io/wiki/GoF-Design-Pattern) 책에 나오는 [팩토리 메서드 패턴](https://johngrib.github.io/wiki/pattern/factory-method)과 이름만 비슷할 뿐, **엄연히 다른 패턴이다.**
- Effective Java 저자 Joshua Bloch도 [(책) GoF의 디자인 패턴](https://johngrib.github.io/wiki/GoF-Design-Pattern) 책에 나온 어떤 패턴과도 맞아 떨어지지 않는다며 주의하라고 한다.

#### 💡  **팩토리 메서드 패턴(Factory Method Pattern)** 

> 
> 
> - 정의
>     - 객체 생성을 위한 인터페이스를 정의하고, **어떤 클래스의 인스턴스를 생성**(`new`)할지에 대한 처리는 **서브클래스가 결정**하는 패턴이다.
> - 예시 코드
>     - Spring Framework - Bean Factory:
>             
>       ```java
>       public interface BeanFactory {
>             
>       Object getBean(String name) throws BeansException;
>             
>       <T> T getBean(String name, Class<T> requiredType) throws BeansException;
>             
>       Object getBean(String name, Object... args) throws BeansException;
>             
>       <T> T getBean(Class<T> requiredType) throws BeansException;
>             
>       <T> T getBean(Class<T> requiredType, Object... args) throws BeansException;
>                 
>       //...
>       }
>       ```
>             
>       - Spring Framework는 IoC (Inversion of Control) 컨테이너를 통해 객체의 생명주기를 관리하고 의존성 주입(Dependency Injection)을 제공한다.
>       - 각각의 `getBean()`는 **팩토리 메서드**로, 빈의 이름이나 타입이 일치하는 빈을 반환한다.
>       - 그 후, Spring은 ApplicationContext 인터페이스로 BeanFactory를 확장하여 추가 기능들을 추가하여 구성한다.
>         - ApplicationContext 클래스 구현을 통해 빈(Bean)을 생성하는 다양한 팩토리 메서드를 활용하여 애플리케이션의 객체를 생성하고 관리할 수 있다.
>         - 예를 들어, AnnotationConfigApplicationContext를 사용하면 자바 설정 클래스에 @Bean 어노테이션을 사용하여 빈(Bean)을 정의하고, ApplicationContext가 해당 설정 클래스를 읽어 빈을 생성하고 관리하게 된다.
>                 
>         ![image](https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/77af5ad2-d05d-4199-9919-eae04d400439)
>                 
>     

### 2.  그렇다면 정적 팩토리 메서드 패턴은 무엇이고, 왜 사용해야 하는가?

- 정의
    - **정적 메서드**로 객체 생성을 **캡슐화**하는 패턴이다. 즉, 객체 생성을 정적 메서드에게 위임하는 것이다.
- 특징
    - 일반적으로 정적 팩토리 메서드를 보유한 클래스의 생성자는 `private`으로 선언하여 직접 인스턴스화를 방지하고, `static` 메서드를 통해서만 생성이 가능하다.
        - 다만 Collection Framework의 정적 팩토리 메서드는 불변 객체를 반환하기 때문에 `private` 생성자가 필요하지 않다.
    - `static` 이므로 오버라이드가 불가능하다.
        - JVM 이 메서드를 호출할 때, `instance` 메서드의 경우 런타임 시 해당 메서드를 구현하고 있는 실제 객체를 찾아 호출한다.
        - 하지만 컴파일러와 JVM 모두 `static` 메서드에 대해서는 실제 객체를 찾는 작업을 시행하지 않기 때문에 `class`(`static`) 메서드의 경우, 컴파일 시점에 선언된 타입의 메서드를 호출한다.
- 장점
    - Effective Java 아이템 중 ***생성자 대신 static factory method**를 사용할 수 없는지 생각해 보라* 라는 내용이 있다.
    - 정적 팩토리 메서드는 생성자가 제공해주지 못하는 장점들을 가져다주기도 한다.
        - 가독성
            - 객체를 `new` 키워드를 통해 생성자로 생성하려면 개발자는 해당 생성자의 인자 순서와 내부 구조를 알고 있어야 목적에 맞게 객체를 생성할수가 있다는 번거로움이 있다.
            - 하지만, **반환될 객체의 특성**을 적절하게 표현하는 **메서드 네이밍**으로 반환될 객체의 특성을 유추하도록 도울 수 있다.
        - 다형성
            - 반환하는 객체의 타입을 유연하게 지정할 수 있어 **하위 자료형 객체**를 반환할 수 있다.
            - 또한, ****인자****를 활용하여 ****다양한 객체****를 반환하도록 분기할 수 있다
        - 캡슐화
            - 실제 구현 내용을 **외부에 감추고**, **인스턴스에 대해 통제 및 관리**가 가능하다.
                - 일반적인 객체 생성은 생성자를 통해 객체를 생성하게 되는데, 이 경우 객체 생성에 대한 통제나 관리가 제한적이다.
                - 그러나, 팩토리 메서드를 통해 생성된 객체는 필요에 따라 항상 새로운 객체를 생성하여 반환할 수 있고, 기존에 생성한 객체를 캐싱하여 재사용하게 할 수 있으며, 객체 생성에 필요한 초기화 작업을 메서드 내에서 처리할 수 있다.
        - 오브젝트 풀링
            - 객체 구축 비용이 많이 드는 경우, 객체 구축 후 **재활용**을 통해 Database Connection과 같은 제한된 리소스에 대한 액세스를 제어할 수 있다.

#### 💡 오브젝트 풀링(Object Pooling)이란?

> 
> 
> - 객체를 매번 할당, 해제하지 않고 고정된 크기의 pool에 들어있는 객체를 재사용함으로써 메모리 사용량을 개선시키는 것이다.
> - 대신, 아주 무거운 객체가 아닌 다음에서 객체 생성을 피하려고 커스텀 객체 풀(pool)을 만들지 않는 것이 좋다. DB 커넥션 같은 경우는 생성 비용이 워낙 비싸서 재사용하는 편이 낫지만, 일반적으로 자체 오브젝트 풀은 코드를 헷갈리게 만들고 메모리 사용량을 늘리고 성능을 떨어뜨리기 때문이다.

### 3. 정적 팩토리 메서드 네이밍 컨벤션

- 일반적으로 생성자는 클래스와 동일한 이름을 갖지만, 정적 팩토리 메서드는 그렇지 않다.
    - 즉, 동일한 인수 유형을 허용하는 두 가지 다른 메서드를 가질 수 있다.
- 또한, 정적 팩토리 메서드와 **다른 정적 메서드의 역할을 구분짓기 위해** 독자적인 네이밍 컨벤션(Convention)이 존재한다.
- `from` : 하나의 매개 변수를 받아서 객체를 생성
- `of` : 여러개의 매개 변수를 받아서 객체를 생성
- `getInstance` **,** `instance` : 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음
- `newInstance` **,** `create` : 새로운 인스턴스를 생성
    - 동일한 인수 유형을 허용하는 두 가지 다른 메서드
        
        ```java
        Coordinate c = Coordinate.createFromCartesian(double x, double y)
        
        Coordinate c = Coordinate.createFromPolar(double distance, double angle)
        ```
        
    

### 4. 결론

- 정적 팩토리 메서드는 생성자와 달리 이름을 가질 수 있으며, 객체 생성 로직을 더 자세하게 제어할 수 있다.
- 또한, 생성자와 달리 호출 시마다 새로운 객체를 생성할 필요가 없으며, 객체 재사용이 가능하다는 장점도 있다.
- 따라서, 일반적인 자바 객체 생성 시 위 장점을 활용하고 싶다면, 생성자 대신 정적 팩토리 메서드를 사용하는 것도 고려해보자.
    - 간혹 스프링의 생성자 주입과 정적 메서드가 비교되는 자료가 있다.
    - 그러나, 이는 스프링의 의존성 주입과는 별개로 일반적인 자바 객체를 생성할 때의 이야기이다.
    - 스프링에서는 객체 생성 시 주로 생성자 주입(Constructor Injection)을 사용하며, 빈(Bean)으로 등록된 객체는 스프링 컨테이너가 관리하기 때문에, 이러한 스프링의 IoC(Inversion of Control) 개념을 깨뜨리면서까지 정적 팩토리 메서드를 사용하는 것은 스프링을 사용하는 의미가 없기 때문이다.

### References

- 팩토리 패턴, 팩토리 메서드 패턴
    - [All about Java — Part1(a)](https://medium.com/@adititripathi694/all-about-java-part1-a-38283e76b799)
    - [팩토리 패턴(Factory Pattern)](https://www.crocus.co.kr/1543)
    - [팩토리 패턴 (팩토리 메서드 패턴, 추상 팩토리 패턴)](https://fvor001.tistory.com/63)
    - [Factory 패턴 (2/3) - Factory Method (팩토리 메서드) 패턴](https://bcp0109.tistory.com/367)
    - [팩토리 메서드 패턴](https://refactoring.guru/ko/design-patterns/factory-method)
    - [Spring에서 팩토리 메서드 패턴 사용 예](https://velog.io/@weekbelt/%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9CFactory-Method-%ED%8C%A8%ED%84%B4)
- 정적 팩토리 메서드 패턴
    - [정적 팩토리 메서드(static factory method) *](https://johngrib.github.io/wiki/pattern/static-factory-method/)
    - [정적 팩토리 메서드 패턴 (Static Factory Method)](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%83%9D%EC%84%B1%EC%9E%90-%EB%8C%80%EC%8B%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EC%9E%90)
    - [정적 팩토리 메서드(Static Factory Method)는 왜 사용할까?](https://tecoble.techcourse.co.kr/post/2020-05-26-static-factory-method/)
    - [Item01. 생성자 대신 static factory method를 사용할 수 없는지 생각해 보라 <이펙티브자바>](https://bactoria.tistory.com/entry/Item01-%EC%83%9D%EC%84%B1%EC%9E%90-%EB%8C%80%EC%8B%A0-static-factory-method%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%A0-%EC%88%98-%EC%97%86%EB%8A%94%EC%A7%80-%EC%83%9D%EA%B0%81%ED%95%B4-%EB%B3%B4%EB%9D%BC-%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C%EC%9E%90%EB%B0%94)
    - [[StackOverFlow] What are static factory methods?](https://stackoverflow.com/questions/929021/what-are-static-factory-methods)
    - [Spring - bean 주입과 static 메서드](https://codemanager.tistory.com/1)
- 오브젝트 풀링
    - [Object Pool, 오브젝트 풀, 객체 풀 [디자인패턴](최적화)](https://luv-n-interest.tistory.com/1116)
- 정적 메서드
    - [[JAVA] 정적 메소드를 오버라이드 하지 못하는 이유](https://hsik0225.github.io/java/2020/12/17/Static-Override/)

## Q2. **Arrays.asList() vs List.of()**

### 1. **리스트 변경 가능 여부**

- `Arrays.asList()` **:**  원소 추가/삭제는 불가능하지만, `set()` 메서드를 통해 기존 원소의 값을 변경할 수 있다.
    - Arrays 클래스 내부에 구현된 또다른 별개의 ArrayList 객체 반환하는데, 해당 구현 클래스 내에 add(), remove()는 없지만, set()은 구현되어있기 때문이다.
- `List.of()` : 원소 추가/삭제와 `set()` 메서드를 통한 변경 모두 불가능하다.
    - 이 리스트는 **불변(immutable)** 특성을 띄는 ImmutableCollections 객체의 내부 클래스인 ListN 객체 반환하기 때문이다.

### 2. **리스트 내부 배열 참조 여부**

- `Arrays.asList()` **:**  원본 배열을 참조하므로, 원본 배열의 값이 변경되면 리스트의 값도 변경되고, 리스트의 값을 수정해도 원본 배열의 값도 바뀐다. [주소값 복사, Shallow Copy]
- `List.of()` : 원본 배열을 참조하지 않고 요소를 일일히 복사하므로, 원본 배열의 값이 변경되어도 리스트의 값은 바뀌지 않는다.

### 3. ****NULL 값을 가질수 있는 여부****

- `Arrays.asList()` **:**  Null 값 가질 수 있다.
    - 기존 배열을 복사할 때, 매개변수인 기존 배열이 Null인지만 검사하기 때문이다.
- `List.of()` : Null 값 가질 수 없다.
    - 요소를 일일히 복사할 때 Null인지 검사하기 때문이다.

### 4. ****메모리 사용량 차이****

- 일반적으로 변경 불가능한 컬렉션 인스턴스는 JVM 내에서 캐싱될 수 있기 때문에 변경 가능한 컬렉션 인스턴스보다 훨씬 적은 메모리를 사용한다.
- 메모리 측면에서 권장되는 메서드 : `Arrays.asList()` < `List.of()`

### 5. 결론

- 일부 가변적인 특성을 지닌 `Arrays.asList()` 와 달리, `List.of()`는 완전 불변하기 때문에 스레드에 안전하다. (Thread-Safe)
- 그러나, 추가/변경/삭제에 자유로운 리스트를 생성하려면 아래처럼 `new ArrayList<>()` 로 새로운 `ArrayList` 객체를 생성해야 한다.
    
    ```java
    String[] arr = { "A", "B", "C" };
     
    // Shallow Copy
    List<String> list = Arrays.asList(arr);
     
    // Deep Copy (권장)
    List<String> list = new ArrayList<>(Arrays.asList(arr));
    ```
    

### References

- [Arrays.asList() 와 List.of() 차이 한방 정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-ArraysasList-%EC%99%80-Listof-%EC%B0%A8%EC%9D%B4-%ED%95%9C%EB%B0%A9-%EC%A0%95%EB%A6%AC)
- [Java : 배열 ↔️ 리스트 변환 (int형 배열 포함)](https://dev-youngjun.tistory.com/240)

## Q3. **Iterator, Entry 의 특징과 주요 메서드는 어떤것이 있을까?**

### 1. Iterator

- 정의
    - Collections Framework의 컬렉션에 저장되어 있는 원소를 순회하는 인터페이스
- 특징
    - 순차적 접근
    - 변경 감지
        - 순환(iteration) 도중 컬렉션이나 리스트의 `remove()` 를 사용하면 `ConcurrentModificationException` 발생시킴
        
        → 따라서, 컬렉션 요소를 제거할 때는 Iterator의 `remove()` 를 사용해야 함
        
    - 순회 종료 후 재사용 불가능
- 주요 메서드
    
    
    | 메서드 | 설명 |
    | --- | --- |
    | hasNext() | 읽어올 요소가 있으면 true, 없으면 false 리턴 |
    | next() | 다음 데이터를 리턴 |
    | remove() | next()로 리턴받은 요소를 삭제 |

### 2. Map.Entry

- 정의
    - Map 인터페이스의 내부 인터페이스(inner interface)로서, Map의 요소인 Key-Value 쌍을 표현하는 데 사용
- 특징
    - Key-Value 쌍을 표현
        - 많은 양의 데이터를 가져올 때 keySet() 메소드를 이용해 모든 key를 불러온 뒤 하나하나 value를 찾는 과정은 많은 시간과 자원이 소모되기 때문에 Key와 Value 모두 필요하다면 entrySet()이 권장된다.
        
        많은 시간과 자원이 소모됩니
        
    - Value 변경 가능
- 주요 메서드
    
    
    | 메서드 | 설명 |
    | --- | --- |
    | boolean equals(Object o) | 동일한 Entry인지 비교 |
    | Object getKey() | Entry의 key 객체 반환 |
    | Object getValue() | Entry의 value 객체 반환 |
    | int hashCode() | Entry의 해시코드 반환 |
    | Object setValue(Object value) | Entry의 value 객체를 지정된 객체로 변경 |

#### 💡 Fast-Fail vs Fail-Safe

> 
> 
> - 자바 컬렉션은 **fail-safe**와 **fail-fast** 2가지 Iterator 타입이 있다.
> - **fail-safe**는 컬렉션이 순환 도중에 변경이 가능한 경우이고, **fail-fast**는 컬렉션이 순환 도중에 변경이 불가능한 경우이다.
> - 즉, 비동기적인 작업 중에 동시적인 변경을 보장하지 못하기 때문에 **fail-fast**를 해결하기 위해서는, 복사본을 사용하거나, 동기화를 보장하는 컬렉션(e.g.`ConcurentHashMap`)을 사용하면 순환 도중에 추가 혹은 삭제해도 오류를 던지지 않는다.

### 3. Map을 순회할 때 Iterator vs Map.Entry?

```java
// Iterator
Iterator<String> keys = map.ketSet().iterator();
while(keys.hasNext()) {
	System.out.println("(key, value) = " + "(" + key +"," + value + ")"); 
}

// Map.Entry
for(Map.Entry<String, String> entry : map.entrySet())	{
	System.out.println("(key, value) = " + "(" + entry.getKey() +"," + entry.getValue() + ")"); 
}
```

- Map 순회 시 Iterator를 사용하면 iterator로 key값을 미리 받아놓아야 한다.
- 두 코드의 속도 차이는 거의 없다.
- Map 원소 삭제 작업
    - `for-each` 루프에서는 `Iterator`가 내부적으로 사용되기 때문에, `for-each` 루프에서는 `Map`의 원소를 직접 삭제할 수 없다.
    - 따라서, `for-each` 루프를 사용할 때는 `Map.Entry`를 사용하여 삭제 작업을 해야한다.

### References

- [[Java] 해시맵, HashMap 사용법과 EntrySet, Iterator](https://lasbe.tistory.com/63)
- [[JAVA]Map.Entry 인터페이스](https://junecode.tistory.com/113)
- [Fail-Fast vs Fail-Safe](https://escapefromcoding.tistory.com/138)
