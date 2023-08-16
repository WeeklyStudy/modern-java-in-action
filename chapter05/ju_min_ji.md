## Q1. **flatMap이란 무엇이고, 어떤 상황에서 사용하면 좋을까?**

### 1. flatMap이란?

`중첩 구조`를 `한 단계 제거`하기 위한 중간 연산이다. 

- 예시
    - `중첩` 리스트가 있을 때, 아래와 같이 flatMap()을 이용하여 이를 `1차원`으로 차원을 낮출 수 있다.
        - flatMap() 사용 전
            
            ```java
            // [[a], [b]]
            List<List<String>> list = Arrays.asList(Arrays.asList("a"), Arrays.asList("b"));
            ```
            
        - flatMap() 사용 후
            
            ```java
            // [a, b]
            List<String> flatList = list.stream()
              .flatMap(Collection::stream)
              .collect(Collectors.toList());
            ```
            
        
### 2. flatMap 활용하기

#### 2-1. 고차원(혹은 중첩) 컬렉션 평탄화 

map() 과 마찬가지로 변환(transforming)에 가까운 매핑(mapping)을 하는 것은 유사하지만, **flatMap()** 은 스트림의 각 `값`을 다른 스트림으로 `변환` 후, 모든 스트림을 하나의 스트림으로 `결합`한다. 이것이 바로 '평탄화'(flatten)(고차원 → 저차원) 작업이다.

즉, map()과 flatmap()의 가장 큰 차이점은 flatmap()은 `결합` 에 포커스를 맞춘다는 것이다.

- **[예시 1] 각 단어를 문자로 분리하여 스트림으로 변환하기**
    - 각 코드의 4번째 라인에서 map()은 `중첩`된 스트림을 반환하는 반면, flatMap()은 `평탄화`된(flattened) 스트림을 반환한다.
    - map() 활용
        
        ```java
        List<String> words = List.of("Hello", "World");
                List<Stream<String>> wordSteams = words.stream() // Stream<String>
                        .map(word -> word.split("")) // Stream<String[]>
                        .map(Arrays::stream) // Stream<Stream<String>>
                        .distinct()
                        .collect(toList());
            }
        ```
        
    - flatMap() 활용
        
        ```java
        List<String> words = List.of("Hello", "World");
                List<String> wordSteams = words.stream() // Stream<String>
                        .map(word -> word.split("")) // Stream<String[]>
                        .flatMap(Arrays::stream) // Stream<String>
                        .collect(toList());
            }
        ```
 
- **[예시 2] 객체 컬렉션 내부의 값에 접근하기 - 특정 날짜 이후에 접속한 유저의 ip 조회**
    - List<AccessLog>는 AccessLog보다 한 차원 높게 있으므로, AccessLog를 `수평`하게 갖는 Stream을 생성하기 위해서는 flatMap 메서드를 사용할 수 있다. 
    - 이후, 각 AccessLog를 String인 ip로 `일대일 변환` 작업에는 map 메서드를 사용할 수 있다.
       ```java
       public List<String> getIpByAccessedAtAfter(List<User> users, LocalDate accessedAt) {
           return users.stream()
                   .map(User::accessLogs) //  Stream<List<AccessLog>>
                   .flatMap(list -> list.stream()
                           .filter(log -> log.accessedAt().isAfter(accessedAt)) // Stream<AccessLog>
                           .map(AccessLog::ip)) // Stream<String>
                   .collect(Collectors.toList()); // List<String>
       }
       ```

#### 2-2. Optional 값이 존재하는 요소들만 추출

또한 flatMap을 이용하면 계층 구조를 지닌 클래스에서 Null 검사와 관련된 코드를 보다 가독성있게 사용할 수 있다. 

- 예시
    - 아래와 같은 계층구조(Outer 클래스는 Nested 클래스를, Nested 클래스는 Inner 클래스를 포함)가 있다고 할 때, flatMap을 사용하지 않으면 Outer로부터 String foo를 꺼내기 위해 null 검사코드가 반복된다.
        
        ```java
        class Outer {
            Nested nested;
        }
        
        class Nested {
            Inner inner;
        }
        
        class Inner {
            String foo;
        }
        ```
        
       - flatMap 사용 전
          ```java
          Outer outer = new Outer();
          if (outer != null && outer.nested != null && outer.nested.inner != null) {
              System.out.println(outer.nested.inner.foo);
          }
          ```    
       - flatMap 사용 후
          ```java
          Optional.of(new Outer())
               .flatMap(o -> Optional.ofNullable(o.nested))
               .flatMap(n -> Optional.ofNullable(n.inner))
               .flatMap(i -> Optional.ofNullable(i.foo))
               .ifPresent(System.out::println);
          ```

#### 💡 flatMap() vs map()
```java
     <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);
     
     <R> Stream<R> map(Function<? super T, ? extends R> mapper);

```
> - flatMap()은 스트림의 요소에 **일대다 변환**을 적용하고, 그 결과 요소들을 새로운 스트림으로 **평탄화**한다. (The flatMap() operation has the effect of applying a one-to-many transformation to the elements of the stream, and then flattening the resulting elements into a new stream.
Examples.)
> - 반면, map()은 스트림의 요소에 **일대일 변환**을 수행한다.

### 3. 결론
- `변환` 작업만 필요한 경우에는 map()을 사용하는 것이 적절하다.
   - 각 요소를 일대일로 변환하고 원래 컬렉션의 구조를 유지할 수 있기 때문이다.
- 반면, `변환`과 `결합` 작업을 동시에 처리해야 하는 경우에는 flatMap()을 사용하는 것이 유용하다
    - 컬렉션의 각 요소를 변환하고, 그 변환된 요소들을 결합하여 `평평한` 새로운 컬렉션을 생성할 수 있기 때문이다.
    

### References
- [[Java] Stream API의 고급 활용 및 사용 시의 주의할 점 - (4/5) *](https://mangkyu.tistory.com/115)
- [[Stream API] 중간 연산 - flatMap 메서드 *](https://dev-kani.tistory.com/33)
- [[Java 8] Stream - 4 (flatMap, collect)](https://ocwokocw.tistory.com/58)
- [자바 map 메서드와 flatMap 메서드의 차이](https://madplay.github.io/post/difference-between-map-and-flatmap-methods-in-java)
- [[StackOverFlow] What's the difference between map() and flatMap() methods in Java 8?](https://stackoverflow.com/questions/26684562/whats-the-difference-between-map-and-flatmap-methods-in-java-8)

## Q2. **findFirst()와 findAny()의 차이점은 무엇이고 언제 사용하는게 좋을까?**

### 1. findAny와 findFirst

- `findAny` : **it is free to select any element in the stream.** 말 그대로 딱 발견한 값
- `findFirst` : **strictly** the first element of the stream. 무조건 스트림의 첫 요소

### 2. 병렬에서의 차이점

- Stream을 직렬로 처리할 때 `findFirst()`와 `findAny()`는 동일한 요소를 리턴하며 차이점이 없지만, Stream을 병렬로 처리할 때는 차이가 있다.
- `findFirst()`는 병렬 스트림에서도 항상 **스트림의 첫 번째 요소**를 반환하지만, 병렬 스트림에서는 여러 스레드에서 동시에 요소를 처리하므로 `findAny()`는 그 중 **가장 먼저 처리한 임의의 요소**를 반환한다.
- 따라서, 병렬 스트림에서는 `findAny()` 메서드가 더 효율적으로 요소를 반환할 수 있다.

### 3. 결론

- findAny()와 findFirst() 둘 다 요소를 탐색하는 메서드이지만, 결과의 일관성과 요소의 순서에 대한 필요성에 따라 사용하는 것이 좋다.
-  병렬 스트림에서 **일관된 결과와 요소의 순서**가 필요하다면 `findFirst()`를, 그렇지 않다면 조금 더 효율적인 `findAny()`가 권장될 것이다.

### References

- [순차적 스트림, 병렬 스트림 그리고 findAny와 findFirst 에 대해](https://bepoz-study-diary.tistory.com/344)
- [Java - Stream findAny()와 findFirst()의 차이점](https://codechacha.com/ko/java8-stream-difference-findany-findfirst/)

## Q3. **상태가 있음과 없음은 병렬 처리에서 어떤 연관성이 있을까?**

### 1. 가변, 불변 상태 객체와 병렬 처리
책에서는 가변(mutable), 불변(immutable) 상태와 병렬처리에 대해 이와 같이 말한다.

“***스트림***을 ***병렬***로 ***처리***하면서 올바른 결과를 얻으려면 불변 상태 기법을 고수해야 한다.” (p195)

병렬 스트림은 여러 개의 쓰레드에서 반복을 나누어 수행하기 때문이다. 멀티 쓰레드이므로 공유자원 동기화 문제가 발생하기 때문에 만약 mutate state가 있는 객체가 있다면, 각 코어 별로 할당된 쓰레드가 동일한 메모리 영역에 접근하여 값을 서로 바꾸려고 할 것이다.

따라서, 스트림을 병렬 처리하는 경우, 스레드 간의 독립성과 상태 공유에 주의해야 한다.

### 2. 상태가 있는, 상태가 없는 연산과 병렬 처리

그러나, 상태가 있는 연산(stateful operation)에서는 고려해야 할 점이 한 가지 더 있다.

병렬 계산에서 상태가 있는 연산을 포함하는 일부 파이프라인은 데이터에 대한 여러 단계(pass)가 필요하거나 중요한 데이터를 버퍼링(buffering)(나중에 사용하기 위해 버퍼에 임시 저장)해야 할 수 있다.

따라서, 상태가 있는 연산을 병렬 처리하는 경우, 최종 연산 전에 중간 연산은 지연된다는 스트림의 특성으로 인해 다소 비효율적인(nasty) 작업이 될 수 있기 때문에 주의해야 한다.


### References

- [https://velog.io/@injoon2019/스트림은-항상-좋을까](https://velog.io/@injoon2019/%EC%8A%A4%ED%8A%B8%EB%A6%BC%EC%9D%80-%ED%95%AD%EC%83%81-%EC%A2%8B%EC%9D%84%EA%B9%8C)
- [[StackOverFlow] Some of the Stateful Intermediate Operation in Java 8 Stream are still Lazy seeking](https://stackoverflow.com/questions/58383060/some-of-the-stateful-intermediate-operation-in-java-8-stream-are-still-lazy-seek)
- [[StackOverFlow] Will parallel stream work fine with distinct operation?](https://stackoverflow.com/questions/53645037/will-parallel-stream-work-fine-with-distinct-operation)
