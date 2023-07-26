## Q1. **컬렉터를 사용하는 것과 reduce 메서드를 사용하는 것의 차이점은 무엇일까?**

### 1. **Reduction(reduce())**

- 연산의 성격
    - 스트림의 원소들을 차례대로 이항 연산 적용하는 접기([fold](https://en.wikipedia.org/wiki/Fold_%28higher-order_function%29)) 작업이다.
- reduce() 파라미터
    
    
    | Parameter | Description |
    | --- | --- |
    | accumulator | 각 요소가 올 때마다 중간 결과를 생성 |
    | identity | 계산을 위한 초기값으로 스트림이 비어서 계산할 내용이 없더라도 이 값은 리턴 |
    | combiner | 병렬(parallel) 스트림에서 스레드 별로 계산한 결과를 하나로 병합(merge) |
    - **파라미터가 2개인 reduce()**
        - 메서드 시그니처
            
            ```java
            T reduce(T identity, BinaryOperator<T> accumulator)
            ```
            
        - 작동방식
            
            ```java
            T result = identity;
                 for (T element : this stream)
                     result = accumulator.apply(result, element) // result에 element 결합
                 return result;
            ```
            
    - **파라미터가 1개인 reduce()**
        - 메서드 시그니처
            
            ```java
            Optional<T> reduce(BinaryOperator<T> accumulator)
            ```
            
        - 작동 방식
            
            ```java
            boolean foundAny = false; // 현재까지 어떤 요소를 찾았는지를 나타내는 flag
                 T result = null;
                 for (T element : this stream) {
                     if (!foundAny) {
                         foundAny = true;
                         result = element;
                     }
                     else
                         result = accumulator.apply(result, element); // result에 element 결합
                 }
                 return foundAny ? Optional.of(result) : Optional.empty();
            ```
            
            - 시작값인 첫번째 인수가 없기 때문에, 빈 스트림값이 넘겨졌을 때 시작값이 설정되지 않는 상황이 벌어진다. 따라서 한 개의 인수를 갖는 reduce 메서드는 Optional<T> 객체를 반환한다.
    - **파라미터가 3개인 reduce()**
        - 메서드 시그니처
            
            ```java
            <U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)
            ```
            
        - 작동방식
            
            ```java
            U result = identity;
                 for (T element : this stream)
                     result = accumulator.apply(result, element)
                 return result;
            ```
            

### 2. Collector(collect())

- 연산의 성격
    - Collection이 생성되고 각 요소가 해당 컬렉션에 추가되는 집계(aggregation) 작업이다.
- collect() 파라미터
    
    
    | Parameter | Description |
    | --- | --- |
    | supplier | R 타입(중간 축적 타입, intermediate accumulation type) 인스턴스를 생성 |
    | accumulator | R 인스턴스에 T 타입 컬렉션 아이템들을 하나하나 축적 |
    | combiner | 병렬(parallel) 스트림에서 스레드 별로 나눠 계산한 결과를 하나로 병합(merge) |
    - **파라미터가 2개인 collect()**
        - 메서드 시그니처
            
            ```java
            <R> R collect(Supplier<R> supplier, BiConsumer<R,? super T> accumulator, BiConsumer<R,R> combiner)
            ```
            
        - 작동방식
            
            ```java
            R result = supplier.get();
                 for (T element : this stream)
                     accumulator.accept(result, element);
                 return result;
            ```
            
    - **파라미터가 2개인 collect()**
        - 메서드 시그니처
            
            ```java
            <R,A> R collect(Collector<? super T,A,R> collector)
            ```
            
            - 자주 사용되는 컬렉터는 **`Collectors`** 유틸리티 클래스에서 제공되며, 사용자 정의 컬렉터를 만들 수도 있다.
                - [Collectors 정적 팩토리 메서드](https://cornswrold.tistory.com/548)
        - 작동방식
            - 컬렉터(**`collector`**)에 지정된 방식에 따라 결과 컨테이너(**`R`**)에 요소들을 누적하여 컬렉션을 생성한다.

### 3. reduce() vs collect() 차이

- 동작 방식
    - `reduce()` : 스트림의 요소를 순차적으로 리듀스하여 최종 결과를 얻는 단순한 리듀싱 기능으로, 스트림의 요소를 이용하여 `하나의 값으로 줄이는 연산`을 수행한다.
    - `collect()` : 스트림의 요소를 수집하여 최종 결과를 얻기 위해 내부적으로 리듀싱 연산을 수행하지만, 단순 리듀싱 기능과 달리, 스트림의 요소를 `누적하는 컨테이너를 생성하고 요소를 컨테이너에 추가`하는 과정을 거친 후, 최종 결과를 도출한다.
- 상태 변화
    - `reduce()` : 두 개의 `불변`(immutable) `값을 결합하여 새 값을 생성`한다. [불변 축소 작업]
    - `collect()` : 결과를 축적하기 위해 `컨테이너 변경`하도록(mutate a container) 설계 [가변 축소 작업]

### 4. 결론

- `collect()` 는 리듀싱 연산을 병렬로 수행하는데에 적합하도록 설계되어있다.
    - 컬렉터를 이용하여 스트림의 요소를 누적하는 과정에서 새로운 컨테이너를 만들어 항상 새로운 값을 반환하기 때문에 스레드 간에 동기화가 필요하지 않기 때문이다.
- 또한, 다양한 그룹화, 필터링, 요약 등의 복잡한 수집 기능이 필요한 경우에는 컬렉터를 사용하여 해당 로직을 더욱 추상화하고 재사용성을 높일 수 있다.
- 반면, `reduce()`는 병렬 작업이 필요하지 않거나 단순 리듀스 기능만 필요할 때 메모리를 더 효율적으로 사용할 수 있게 한다.
    - 중간 결과를 추가하지 않고 최종 결과만 도출하기 때문이다.

### References

- [java 공식 문서](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/util/stream/Stream.html#collect(java.util.function.Supplier,java.util.function.BiConsumer,java.util.function.BiConsumer))
- [Java Stream collect() Method Examples](https://www.digitalocean.com/community/tutorials/java-stream-collect-method-examples)
- [[StackOverFlow] Java 8 Streams - collect vs reduce](https://stackoverflow.com/questions/22577197/java-8-streams-collect-vs-reduce)
- [stream 과 parallel stream, reduce 와 collect 의 차이와 사용법](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=woong17&logNo=221268337085)

## Q2. **collect vs Collector vs Collectors vs Collection**

### 1. 용어 구분

- `Collector` : 컬렉션에 요소를 축적하고 다양한 기준에 따라 요소를 요약하는 등의 가변 축소 작업을 제공하는 **인터페이스** (*A mutable reduction operation.*)
- `Collectors` : **인터페이스의 구현**을 제공하는 클래스로, static 팩토리 메서드를 모아놓은 유틸리티 클래스이다. (*Implementations of `Collector`)*
- `collect()` : 스트림의 요소를 컬렉터를 사용하여 원하는 형태의 컬렉션으로 변환하거나, 요약 정보를 얻거나, 그룹화하는 등 다양한 작업을 수행하는 **Stream API의 최종 연산 메서드** 중 하나
- `Collection` : 리스트(List), 세트(Set), 큐(Queue) 등의 다양한 컬렉션 타입들을 정의하고, 이들 컬렉션 타입들의 공통 기능들을 제공하는 인터페이스로, **컬렉션 프레임워크를 구현한 인터페이스**

#### 💡 가변 축소 작업(Mutable Reduction Operation)이란?

> 가변 축소 작업은 스트림 요소를 처리한 다음 변경 가능한 결과 컨테이너(Container)에 누적한다(accumulate). 요소가 처리되면 결합 메서드 Combiner가 모든 결과 컨테이너를 병합하여 결과를 생성한다.
> 

#### 💡 팩토리 메서드(Factory Method)란?

> 클래스 디자인 패턴 중 하나인 Factory Method 패턴은 객체를 생성하는 일반적인 방법인 생성자(Constructor) 대신, 클래스의 정적(static) 메서드를 통해 객체를 생성하는 디자인 패턴이다.
> 

> 정적 팩토리 메서드는 위 패턴에서 유래한 용어로, ****객체 생성의 역할을 하는 클래스 메서드****이다.
> 

```java
// LocalTime.class
...
public static LocalTime of(int hour, int minute) {
  ChronoField.HOUR_OF_DAY.checkValidValue((long)hour);
  if (minute == 0) {
    return HOURS[hour];
  } else {
    ChronoField.MINUTE_OF_HOUR.checkValidValue((long)minute);
    return new LocalTime(hour, minute, 0, 0);
  }
}
...

// of 메서드를 통해 LocalTime 객체를 생성하는 아래 코드는 팩토리 메서드 패턴을 사용했다.
LocalTime openTime = LocalTime.of(9, 30);
```

#### 💡 컬렉션 프레임워크(Collection Framework)?

> - 자바에서 컬렉션 프레임워크(collection framework)란 다수의 데이터를 쉽고 효과적으로 처리할 수 있는 표준화된 방법을 제공하는 클래스의 집합을 의미한다.
> 
> - 즉, 데이터를 저장하는 자료 구조와 데이터를 처리하는 알고리즘을 구조화하여 클래스로 구현해 놓은 것이다.
> - 이러한 컬렉션 프레임워크는 자바의 인터페이스(interface)를 사용하여 구현한다.
> - 컬렉션 프레임워크에서 핵심이 되는 인터페이스는 아래와 같다.
>     - List 인터페이스
>     - Set 인터페이스
>     - Map 인터페이스
> - List와 Set 인터페이스는 모두 Collection 인터페이스를 상속받지만, 구조상의 차이로 인해 Map 인터페이스는 별도로 정의한다.
> 
> ![image](https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/6e169ce8-c787-4750-bcb3-da21d3a90e0b)
> 

### 2. 결론

- `collect()`는 스트림의 최종 연산 중 하나로, `Collector` 인터페이스를 구현한 객체를 사용하여 스트림의 요소를 수집하는 역할을 한다.
- 이러한 `Collector` 인터페이스를 구현한 기본 컬렉터들은 `Collectors` 클래스에서 정적 팩토리 메서드로 제공되며, 스트림의 요소를 `Collection` 인터페이스를 구현한 컬렉션 타입으로 변환하는 데 사용된다.

### References

- [Java 8 Collectors - javatpoint](https://www.javatpoint.com/java-8-collectors)
- [[StackOverFlow] What the difference between Java8 Collectors and Collector?](https://stackoverflow.com/questions/41906489/what-the-difference-between-java8-collectors-and-collector)
- [컬렉션 프레임워크의 개념 - 코딩의 시작, TCP School](http://www.tcpschool.com/java/java_collectionFramework_concept)
- [[Java] 자바 컬렉션(Collection)이란?](https://choicode.tistory.com/25)
- [정적 팩토리 메서드(Static Factory Method)는 왜 사용할까?](https://tecoble.techcourse.co.kr/post/2020-05-26-static-factory-method/)

## Q3. **groupingBy 와 partitioningBy의 유사점과 차이점은 무엇일까?**

### 1. **그룹화 메서드 groupingBy()**

```java
//Collector groupingBy(Function classifier)
groupingBy(Function<? super T, ? extends K>)

//Collector groupingBy(Function classifier, Collector downstream)
groupingBy(Function<? super T, ? extends K>, Collector<? super T, A, D>)

//Collector groupingBy(Function classifier, Supplier mapFactory, Collector downstream)
groupingBy(Function<? super T, ? extends K>, Supplier<M>, Collector<? super T, A, D>)
```

- 그룹화는 `Function`을 분류함수로 사용하여 스트림의 요소를 특정한 기준으로 그룹화하는 기능이다.

### 2. 분할 메서드 partitioningBy()

```java
//Collector partitioningBy(Predicate predicate)
partitioningBy(Predicate<? super T>)

//Collector partitioningBy(Predicate predicate, Colelctor downstream)
partitioningBy(Predicate<? super T>, Collector<? super T, A, D>)
```

- 분할은 `Predicate`를 분류함수로 사용하는 특수한 그룹화 기능이다.
- 분할 함수는 불리언을 반환하므로 맵의 키 형식은 Boolean이다.
- 결과적으로 그룹화 맵은 최대 두 개의 그룹(True/ False)로 분류된다.

### 3. **groupingBy 와 partitioningBy의 유사점과 차이점**

- 유사점
    - 둘 다 스트림의 요소를 분류하여 그룹화하는 기능을 제공한다.
    - 결과로 맵(Map)을 반환한다.
- 차이점
    - `groupingBy()`는 두 개 이상의 다중 그룹으로 나눌 수 있는 것과 달리, `partitioningBy()`는 두 개의 그룹(T/F)으로 나눌 수 있다.

### 4. 결론

- 다중 그룹으로 분류하고 싶을 때는 `groupingBy()`를 사용하고, 다수준 그룹화에서 두 가지 그룹으로만 나뉘어도 되는 경우는 `partioningBy()` 를 사용하면 더욱 간결하게 Map을 구현할 수 있다.

### Reference

- [java stream partitioningBy(), groupingBy() 분할과 그룹화](https://wildeveloperetrain.tistory.com/272)
