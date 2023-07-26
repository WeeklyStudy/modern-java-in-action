# https://github.com/WeeklyStudy/modern-java-in-action/issues/17 collect vs Collector vs Collectors vs Collection

## 💡collect

> Collector를 매개변수로 하는 스트림의 **최종연산**이다.
> 

인수로 전달되는 객체의 구현 방법대로 **스트림의 요소를 수집**한다.

1. **Collector 객체**를 인수로 넘기는 방법
    1. Collectors 에 정의된 정적 팩토리 메서드를 전달하는 방식([예제](https://devdocs.programmers.co.kr/references/java/docs/api/java.base/java/util/stream/Stream.html#collect(java.util.stream.Collector)))
    2. Collector 인터페이스를 직접 구현한 클래스를 생성하여 전달하는 방식
2. 수집에 대한 핵심 로직을 구현하는 **supplier, accumulator, combiner 메서드**를 인수로 넘기는 방법([예제](https://devdocs.programmers.co.kr/references/java/docs/api/java.base/java/util/stream/Stream.html#collect(java.util.function.Supplier,java.util.function.BiConsumer,java.util.function.BiConsumer)))
    - 가독성과 재사용성이 떨어져 잘 쓰이지 않는다.

| 메서드 | 반환 타입 |
| --- | --- |
| collect(Collector<T, A, R> collector) | R |
| collect(Supplier<R> supplier, BiConsumer<R, T> accumulator, BiConsumer<R, R> combiner) | R |

## 💡Collector

> 리듀싱 연산을 어떻게 구현할지를 제공하는 메서드를 정의해놓은 **인터페이스**다.
> 

### Collector<T, A, R>

- `T` : 스트림 요소의 형식
- `A` : 누적자(중간 결과를 누적하는 객체)의 형식
- `R` : collect 연산의 최종 결과 형식

Collector 인터페이스를 구현하는 클래스는 5개의 메서드를 오버라이딩 해야한다.

| 메서드 | 기능 | 반환 타입 | 함수 디스크립터 |
| --- | --- | --- | --- |
| supplier() | 누적자를 만드는 함수를 반환한다. | Supplier<A> | () → A |
| accumulator() | 스트림 요소의 수집 방법을 함수로 반환한다. | BiConsumer<A, T> | (A, T) → void |
| combiner() | 두 부분 누적자를 합치는 방법을 함수로 반환한다.(병렬 스트림에서 사용) | BiOperator<A, A> | A → A |
| finisher() | 누적자를 결과 형식으로 최종 변환할 방법을 함수로 반환한다. 변환이 필요없는 경우 항등함수인 Function.identity()를 반환한다. | Function<A, R> | A → R |
| characteristics() | 컬렉터가 수행하는 작업의 속성 정보를 담아 집합으로 반환한다.(CONCURRENT, UNORDERED, IDENTITY_FINISH) | Set<Characteristics> |  |

## 💡Collectors

> 다양한 기능의 Collector 인터페이스를 구현한 클래스를 정적 팩토리 메서드로 제공하는 클래스다.
> 
- `정적 팩토리 메서드` : 객체 생성을 담당하는 클래스 메서드다.(간접적으로 생성자 호출)
1. 리듀싱
    
    스트림 요소를 하나의 값으로 요약하는 기능이다.
    
    - counting(개수), summingXXX(합계), averagingXXX(평균), maxBy(최댓값), minBy(최솟값), summarizingXXX(통계), joinig(문자열 연결), reducing(범용 리듀싱)
2. 그룹화
    
    스트림의 요소를 특정 기준으로 그룹화하는 기능이다.(MySQL group by 와 비슷)
    
    - groupingBy
3. 분할
    
    스트림의 요소를 지정한 조건에 대해 참,거짓 2개의 그룹으로 분할하는 기능이다.
    
    - partitioningBy
4. 컬렉션/배열로 변환
    
    스트림을 컬렉션이나 배열로 반환하는 기능이다.
    
    - toList, toMap, toCollection

## 💡Collection vs Collections
![Untitled (5)](https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/8f5a5bd0-9af0-494b-b4bd-f79c5329104f)

| Collection | Collections |
| --- | --- |
| Iterable을 상속받는 인터페이스다. | Object를 상속받는 클래스다. |
| Collection Framework의 최상위 인터페이스다. | Collection을 다루기 위한 static 메서드를 제공한다.(Arrays가 배열을 다루기 위한 메서드를 제공하는 것과 같음) |
- `Collection Framework` : 다수의 데이터를 쉽고 효과적으로 처리할 수 있는 표준화된 방법을 제공하는 클래스의 집합

## 💡결론

- `collect()`는 `Collector`를 매개변수로 하는 스트림의 `최종연산`이다.
- `Collector`는 리듀싱 연산(Collector)을 어떻게 구현할지 제공하는 메서드를 정의해놓은 `인터페이스`다.
- `Collectors`는 다양한 기능의 `Collector` 를 구현한 클래스를 정적 팩토리 메서드로 제공하는 `클래스`다.
- `Collection` 은 `Collection Framework`의 최상위 `인터페이스`이다.
- `Collections` 는 `Collection` 을 다루기 위한 메서드를 제공하는 `클래스`다.

## Reference

- [[Java] 스트림(Stream)의 최종 연산 - 요소의 수집 : collect()](https://tychejin.tistory.com/385)
- [collect()와 Collectors](https://mgyo.tistory.com/754)
- [Java 8 : Collector 정리](https://velog.io/@ehdrms2034/Java-8-Collector-%EC%A0%95%EB%A6%AC)
- [Stream(스트림) - 7. Collector 구현 (최종연산 3/3)](https://jamie95.tistory.com/113)
- [Collectors! 데이터를 수집해보자.](https://doohyun.tistory.com/39)
- [[Java] 자바 8 - (3) Stream Collectors](https://jodong.tistory.com/26)
- [Collections 클래스, Collection 정리](https://velog.io/@jyo925/Collections-%ED%81%B4%EB%9E%98%EC%8A%A4)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/18 컬렉터를 사용하는 것과 reduce 메서드를 사용하는 것의 차이점은 무엇일까?

`collect()` 와 `reduce()` 는 스트림 요소들을 리듀스(대량의 데이터를 가공하여 축소하는 것)하는 메서드다. 두 메서드로 같은 기능을 구현할 수 있다. 하지만 

## 💡collect()

결과를 누적하는 컨테이너를 바꾸도록 설계된 `mutable(가변)` 연산이다.

즉, 누적 컨테이너(첫번째 인수)에 두번째 인수를 누적하는 방식이다.(리턴값 없음)

- `BiConsumer<R, T>` ⇒ `(R, T) → void`

| 메서드 | 반환 타입 |
| --- | --- |
| collect(Collector<T, A, R> collector) | R |
| collect(Supplier<R> supplier, BiConsumer<R, T> accumulator, BiConsumer<R, R> combiner) | R |

## 💡reduce()

두 값을 하나로 도출하도록 설계된 `immutable(불변)` 연산이다.

즉, 두 개의 인수로 새로운 객체를 만들어 리턴하는 방식이다. 매번 새로운 객체를 만들면서 성능이 저하될 수 있다.

- `BinaryOperator<T>` ⇒ `(T, T) → T`
- `BiFunction<U, T, U>` ⇒ `(U, T) → U`

| 메서드 | 반환 타입 |
| --- | --- |
| reduce(BinaryOperator<T> accumulator) | Optional<T> |
| reduce(T identity, BinaryOperator<T> accumulator) | T |
| reduce(U identity, BiFunction<U, T, U> accumulator, BinaryOperator<U> combiner) | U |

> 파라미터
> 
> - `collector` : 리듀스 방법에 대한 컬렉터
> - `supplier` : 누적 컨테이너를 생성하는 함수
> - `identity` : 초기값
> - `accumulator` : 리듀스 연산을 수행하는 함수
> - `combiner` : 병렬 스트림에서 연산 결과를 하나로 합칠 때 사용하는 함수

## 💡결론

- `collect()` 는 `가변 연산`이고 `reduce()` 는 `불변 연산`이다.
- 가변 컨테이너 관련 작업이면서 병렬성을 확보하기 위해서 `collect()`로 리듀싱 연산을 구현하는 것이 바람직하다.

## Reference

- [Java - Stream 최종 연산자(reduce)](https://napasun-programming.tistory.com/36)
- [Java - collect(), Collector, Collectors](https://napasun-programming.tistory.com/39)
- [reduce vs collect](https://github.com/woowacourse-study/2022-modern-java-in-action/issues/51)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/19 groupingBy 와 partitioningBy의 유사점과 차이점은 무엇일까?
    
## 💡groupingBy

> 스트림의 요소를 특정한 기준으로 그룹화하는 것이다.
> 

| 메서드 | 반환 타입 | 기능 |
| --- | --- | --- |
| groupingBy(Function)
=groupingBy(Function, Collectors.toList()) | Map<K, List<T>>
- K: Function의 반환 타입 | 하나의 프로퍼티값(Function 반환값)을 기준으로 스트림의 항목을 그룹화하여 HashMap(프로퍼티 값, 리스트)로 반환 |
| groupingBy(Function, Collector) | Map<K, D>
- K: Function의 반환 타입
- D: Collector의 반환 타입 | 하나의 프로퍼티값(Function 반환값)을 기준으로 스트림의 항목을 그룹화하여 HashMap(프로퍼티 값, 특정형태)로 반환 |
| groupingBy(Function, Supplier, Collector) | M extends Map<K, D>
- M: Supplier의 반환 타입
- K: Function의 반환 타입
- D: Collector의 반환 타입 | 하나의 프로퍼티값(Function 반환값)을 기준으로 스트림의 항목을 그룹화하여 특정맵(프로퍼티 값, 특정형태)로 반환 |

## 💡partitioningBy

> 스트림의 요소를 지정한 조건에 일치하는 그룹과 일치하지 않는 그룹으로 분할하는 것이다.
> 

| 메서드 | 반환 타입 | 기능 |
| --- | --- | --- |
| partitioningBy(Predicate) | Map<Boolean, List<T>>
- T: 스트림 요소 타입 | Predicate를 스트림의 각 항목에 적용한 결과를 Map(Boolean값, 리스트)로 반환 |
| partitioningBy(Predicate, Collector) | Map<Boolean, D>
- D: Collector의 반환 타입 | Predicate를 스트림의 각 항목에 적용한 결과를 Map(Boolean값, 특정형태)로 반환 |

## 💡결론

`groupingBy` 와 `partitioningBy` 모두 스트림을 그룹화한다. 다만 아래와 같은 차이가 존재하므로 적절한 경우에 사용해야 한다.

|  | groupingBy | partitioningBy |
| --- | --- | --- |
| 최대 분할 개수 | n분할 | 2분할 |
| 매개변수 | Function(T → R) | Predicate(T → boolean) |
| 2분할 시 속도 | partitioningBy보다 느림 | groupingBy보다 빠름 |

## Reference

- [java stream partitioningBy(), groupingBy() 분할과 그룹화](https://wildeveloperetrain.tistory.com/272)
- [그룹화와 분할 - groupingBy(), partitioningBy()](https://pw4ngc0.tistory.com/entry/%EA%B7%B8%EB%A3%B9%ED%99%94%EC%99%80-%EB%B6%84%ED%95%A0-groupingBy-partitioningBy)
