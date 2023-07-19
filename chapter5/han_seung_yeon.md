# https://github.com/WeeklyStudy/modern-java-in-action/issues/14 findFirst()와 findAny()의 차이점은 무엇이고 언제 사용하는게 좋을까? 
    
## 💡findAny()

> **스트림에서 가장 먼저 탐색되는 요소**를 리턴한다.
> 

## 💡findFirst()

> 조건에 일치하는 요소들 중에서 **스트림에서 순서가 가장 앞에 있는 요소**를 리턴한다.
> 

## 💡findAny() vs findFirst()

### 1. 순차 스트림

순차 스트림에서 `findFirst()`와 `findAny()` 가 항상 동일한 요소를 리턴하므로 결과에 차이가 없다.

### 1.1. findFirst()

```java
// given
List<String> elements = Arrays.asList("a", "a1", "b", "b1", "c", "c1");
// when
Optional<String> first = elements.stream()
        .filter(s -> s.startsWith("b"))
        .findFirst();
// b
System.out.println(first.get());
```

- 결과값: 항상 `b`

### 1.2. findAny()

```java
// given
List<String> elements = Arrays.asList("a", "a1", "b", "b1", "c", "c1");
// when
Optional<String> any = elements.stream()
        .filter(s -> s.startsWith("b"))
        .findAny();
// b
System.out.println(any.get());
```

- 결과값: 항상 `b`

### 2. 병렬 스트림

병렬 스트림에서 `findFirst()`와 `findAny()` 의 결과가 달라질 수 있다.

### 2.1. findFirst()

```java
// given
List<String> elements = Arrays.asList("a", "a1", "b", "b1", "c", "c1");
// when
Optional<String> first = elements.stream()
        .parallel()
        .filter(s -> s.startsWith("b"))
        .findFirst();
// b
System.out.println(first.get());
```

- 결과값: 항상 `b`

### 2.2. findAny()

```java
// given
List<String> elements = Arrays.asList("a", "a1", "b", "b1", "c", "c1");
// when
Optional<String> any = elements.stream()
        .parallel()
        .filter(s -> s.startsWith("b"))
        .findAny();
// b or b1
System.out.println(any.get());
```

- 결과값: `b` 혹은 `b1` (실행할 때마다 리턴값이 달라진다)

## 💡결론

- `순차 스트림`에서는 `findFirst()` 와 `findAny()` 의 결과값이 동일하다.
    - `순차 스트림`에서 `findFirst()` 의 결과값은 항상 `맨 첫번째 요소`다.
    - `순차 스트림`에서 `findAny()` 의 결과값은 항상 `맨 첫번째 요소`다.(단일 스레드로 데이터가 순차적으로 들어오기 때문에)
- `병렬 스트림`에서는 `findFirst()` 와 `findAny()` 의 결과값이 다를 수 있다.
    - `병렬 스트림`에서 `findFirst()` 의 결과값은 항상 `맨 첫번째 요소`다.
    - `병렬 스트림`에서 `findAny()` 의 결과값은 `예상 불가`다.(멀티 스레드로 어떤 데이터가 먼저 들어올지 알 수 없기 때문에)
- `병렬 스트림`에서 순서에 상관없이 요소를 찾는 경우라면 성능상 이점을 위해 `findAny()` 를 사용하자.
- 하지만, 매번 같은 결과가 보장되어야 한다면 `findFirst()` 를 사용하자.
    - 단, 원본 데이터가 `순서가 없는 컬렉션(e.g. HashSet)`인 경우, `findFirst()` 는 동일한 결과값을 보장하지 않는다.
        
        ```java
        // given
        Set<String> set = new HashSet<>();
        set.addAll(Arrays.asList("b", "b1", "c", "c1"));
        
        // when
        Optional<String> any1 = set.stream()
                .findFirst();
        // b
        // [b, c, c1, b1]
        System.out.println(any1.get());
        System.out.println(set);
        //
        set.add("a");
        // when
        Optional<String> any2 = set.stream()
                .findFirst();
        // a
        // [a, b, c, c1, b1]
        System.out.println(any2.get());
        System.out.println(set);
        ```
        

## Reference

- findAny() vs findFirst()
    - [Java - Stream findAny()와 findFirst()의 차이점](https://codechacha.com/ko/java8-stream-difference-findany-findfirst/)
    - [순차적 스트림, 병렬 스트림 그리고 findAny와 findFirst 에 대해](https://bepoz-study-diary.tistory.com/344)
    - [모던 자바 인 액션 스트림에 대해 읽다가 생긴 의문들 (테스트로 해결하기!)*](https://eocoding.tistory.com/106?category=941319)
    - [https://howtodoinjava.com/java8/stream-findfirst-findany/*](https://howtodoinjava.com/java8/stream-findfirst-findany/)
    - https://stackoverflow.com/questions/45907797/understanding-order-of-elements-in-stream-generated-from-hashset
- Encounter Order
    - [Stream Ordering in Java](https://www.baeldung.com/java-stream-ordering)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/15 flatMap이란 무엇이고, 어떤 상황에서 사용하면 좋을까?

## 💡map()이란?

> 단일 원소 스트림의 각 요소에 함수를 적용한 값을 새로운 단일 원소 스트림으로 반환하는 중간연산이다.
> 
- `Stream<T>` → `Stream<R>`

| 반환 타입 | 메서드 | 함수 디스크립터 |
| --- | --- | --- |
| Stream<R> | map(Function<T, R> mapper) | T → R |

> 리스트의 각 요소에 10을 곱한 리스트 반환하기
> 

```java
// given
List<Integer> list = Arrays.asList(1, 2, 3);

// when
List<Integer> collect = list.stream()   // Stream<Integer>
        .map(num -> num * 10)   // Stream<Integer>
        .collect(toList());

// [10, 20, 30]
System.out.println(collect);
```

## 💡flatMap()이란?

> `Array`나 `Collection`으로 감싸진 모든 원소를 **평면화**하여 새로운 단일 원소 스트림으로 반환하는 중간연산이다.
- `Stream<T[]>` → `Stream<T>`
- `Stream<List<T>>` → `Stream<T>`
- `Stream<Set<T>>` → `Stream<T>`

| 반환 타입 | 메서드 | 함수 디스크립터 |
| --- | --- | --- |
| Stream<R> | flatMap(Function<T, <Stream<R>> mapper) | T → Stream<R> |

> 2차원 리스트의 모든 요소들의 중복을 제거한 리스트 반환하기
> 

```java
// given
List<List<Integer>> list = new ArrayList<>();
list.add(Arrays.asList(1, 2));
list.add(Arrays.asList(1, 3));
list.add(Arrays.asList(2, 4));

// when
List<Integer> collect1 = list.stream()   // Stream<List<Integer>>
        .flatMap(l -> l.stream())   // Stream<Integer>
        .collect(toList());

// [1, 2, 1, 3, 2, 4]
System.out.println(collect1);

List<Integer> collect2 = list.stream()   // Stream<List<Integer>>
        .flatMap(l -> l.stream())   // Stream<Integer>
        .distinct()
        .collect(toList());

// [1, 2, 3, 4]
System.out.println(collect2);
```

## 💡결론

- `map()` 은 `단일 원소 스트림`을 어떤 함수를 적용해 새로운 `단일 원소 스트림`으로 만들 때 사용한다.
- `flatMap()` 은 `중첩된 구조의 모든 원소`를 `평면화`하여 `단일 원소 스트림`으로 만들 때 사용한다.

## Reference

- map(), flatMap() 개념
    - [Java8 .map()과 .flatMap()의 차이](https://qkrrudtjr954.github.io/java/2017/10/15/difference-between-map-and-flatMap.html)
- map vs flatMap
    - [자바 map 메서드와 flatMap 메서드의 차이](https://madplay.github.io/post/difference-between-map-and-flatmap-methods-in-java)
    - [[Java] Stream API - map과 flatMap의 차이 및 활용법](https://devjem.tistory.com/41)
    - [[Stream] map() flatMap()*](https://wjjeong.tistory.com/42)
    
# https://github.com/WeeklyStudy/modern-java-in-action/issues/16 상태가 있음과 없음은 병렬 처리에서 어떤 연관성이 있을까?

## 💡Stateless 연산

> 내부 상태를 갖지 않는 연산이다.
> 
- 중간연산: filter, map

## 💡Stateful 연산

> 내부 상태를 갖는 연산이다.
> 
- 중간연산: distinct, skip, limit, sorted
- 최종연산: reduce, sum, max

> 용어 정리
> 
> - `bound` : 연산을 수행하는데 필요한 저장소의 크기가 정해짐
> - `unbounded` : 연산을 수행하는데 필요한 저장소의 크기가 정해지지 않음

## 💡병렬 처리

병렬 처리할 작업이 독립적이지 않다면 수행 성능에 영향을 줄 수 있다.

`sorted()`, `distinct()` 같은 작업을 수행하는 경우에는 **내부적으로 상태에 대한 변수를 각 작업들이 공유**하기 때문에 순차적으로 실행하는 경우가 더 효과적일 수 있다.

### 1. sorted()

정렬이 제대로 되지 않고 순차 처리에 비해 수행 속도도 느렸다.

```java
// given
List<Integer> list = new ArrayList<>();
for(int i = 10; i > 0; i--) {
    list.add(i);
}
// when
long beforeTime = System.currentTimeMillis();
list.parallelStream()
        .sorted()
        .forEach(System.out::println);

long afterTime = System.currentTimeMillis(); // 코드 실행 후에 시간 받아오기
long diffTime = afterTime - beforeTime; // 두 개의 실행 시간
System.out.println("실행 시간(ms): " + diffTime); // 세컨드(초 단위 변환)
```

### 2. distinct()

중복 제거가 제대로 되지만 순차 처리에 비해 수행 속도가 느렸다.

```java
//
List<Integer> list = new ArrayList<>();
for(int i = 100; i > 0; i--) {
    list.add(i % 10);
}
// when
long beforeTime = System.currentTimeMillis();
list.parallelStream()
        .distinct()
        .forEach(System.out::println);

long afterTime = System.currentTimeMillis(); // 코드 실행 후에 시간 받아오기
long diffTime = afterTime - beforeTime; // 두 개의 실행 시간
System.out.println("실행 시간(ms): " + diffTime); // 세컨드(초 단위 변환)
```

## 💡결론

- 병렬 처리는 순서를 보장하지 않는다.
- 독립적인 연산 작업을 할 때 병렬 처리를 사용하자.

## Reference

- [스트림 연산에 있어 상태가 있거나 없다는 의미가 무엇인가?](https://github.com/woowacourse-study/2022-modern-java-in-action/issues/40)
- [Java의 Stream에서 parallelStream은 stream보다 항상 빠를까?](https://twinparadox.tistory.com/627)
- [Java8 Parallel Stream, 성능장애를 조심하세요!](https://m.blog.naver.com/tmondev/220945933678)
- [[JAVA] Stream, 스트림과 병렬처리](https://ict-nroo.tistory.com/43)
