## Q1. **스트림 사용 시 주의할 점에는 무엇이 있을까?**

### 1. 중간 연산 순서 고려

- 스트림의 중간 연산은 파이프라이닝을 통해 연결될 수 있다. 중간 연산의 순서에 따라 결과가 달라지거나 무한 스트림을 초래할 수 있으므로, 연산의 순서를 신중하게 고려해야 한다.
    - 중간 연산 순서가 잘못된 예시
        
        ```java
        IntStream.iterate(0, i -> ( i + 1 ) % 2)
                        .distinct()
                        .limit(10)
                        .forEach(System.out::println);
        System.out.println("complete");
        
        // 최종 결과
        // 0
        // 1
        ```
        
    - 중간 연산 순서가 제대로 된 예시
        
        ```java
        IntStream.iterate(0, i -> ( i + 1 ) % 2)
                        .limit(10)
                        .distinct()
                        .forEach(System.out::println);
        System.out.println("complete");
        
        // 최종 결과
        // 0
        // 1
        // complete
        ```
        

### 2. 최종 연산 누락

- 스트림의 지연 평가는 **최종 연산이 호출될 때까지 실행을 지연**시키기 때문에 중간 연산만으로는 어떠한 동작도 수행하지 않는다. 따라서, 최종 연산을 호출하지 않으면 중간 연산은 아무런 동작도 수행하지 않으므로, 최종 연산을 반드시 호출해야 한다.
    - 최종 연산이 누락된 예시
        
        ```java
        public class LazyStreams {
                private static int length(final String name) {
                    System.out.println("getting length for " + name);
                    return name.length();
                }
        
                private static String toUpper(final String name) {
                    System.out.println("converting to uppercase: " + name);
                    return name.toUpperCase();
                }
            }
        
                public static void main(final String[] args) {
                    List<String> names = new ArrayList<>();
                    names.add("egg");
                    names.add("tomato");
                    names.add("nut");
        
                    Stream<String> namesWith3Letters = names.stream()
                            .filter(name -> LazyStreams.length(name) == 3)
                            .map(name -> LazyStreams.toUpper(name));
        
                    System.out.println("complete");
                }
        
        // 최종 결과
        // complete
        ```
        
    - 최종 연산이 누락되지 않은 예시
        
        ```java
        public class LazyStreams {
                private static int length(final String name) {
                    System.out.println("getting length for " + name);
                    return name.length();
                }
        
                private static String toUpper(final String name) {
                    System.out.println("converting to uppercase: " + name);
                    return name.toUpperCase();
                }
            }
        
                public static void main(final String[] args) {
                    List<String> names = new ArrayList<>();
                    names.add("egg");
                    names.add("tomato");
                    names.add("nut");
        
                    Stream<String> namesWith3Letters = names.stream()
                            .filter(name -> LazyStreams.length(name) == 3)
                            .map(name -> LazyStreams.toUpper(name));
        
                    final String firstNameWith3Letters = namesWith3Letters.findFirst().get();
        
                    System.out.println("output: " + firstNameWith3Letters);
                    System.out.println("complete");
                }
        
        // 최종 결과
        // getting length for egg
        // converting to uppercase: egg
        // output: EGG
        // complete
        ```
        

### 3. 소비된 요소 재사용 불가

- 스트림에서 한 번 소비된 요소는 다시 사용할 수 없다. 이는 스트림의 **불변성**을 유지하기 위한 제약이다. 요소를 다시 사용하려면 원본 데이터에서 새로운 스트림을 생성해야 한다.
    - 재사용 예시
    
    ```java
    public class OneUseStream {
    
        public static void main(String[] args) {
            Stream<String> fruits =Stream.of("apple", "banana", "grape", "orange", "mango");
    
            String longestName = fruits.sorted(Comparator.comparing(String::length)).findFirst().orElse("");
            String shortestName = fruits.sorted(Comparator.comparing(String::length).reversed()).findFirst().orElse("");
            System.out.println("longestName is " + longestName + ", shortestName is " + shortestName);
        }
    }
    
    // IllegalStateException 발생
    // Exception in thread "main" java.lang.IllegalStateException: stream has already been operated upon or closed
    ```
    

### 4. 오토박싱으로 인한 성능 저하

- 자바에서 제네릭은 **참조형**(Reference Type)만 사용 가능한데, **함수형 인터페이스**에는 **제네릭** 파라미터가 사용되기 때문에 오토박싱 오버헤드를 발생시킬 수 있다.
- 기본형 특화 스트림 활용가능한 경우, 이러한 오버헤드 문제를 해결할 수 있다.
    - 오토박싱 예시
        
        ```java
        public long parallelSum(long n) {
            return Stream.iterate(1L, i -> i + 1)
                .limit(n)
                .parallel()
                .reduce(0L, Long::sum);
        }
        ```
        
    - 오토박싱 일어나지 않도록 개선한 예시
        
        ```java
        public long parallelSum(long n) {
            return LongStream.rangeClosed(1, n)
                .parallel()
                .reduce(0L, Long::sum);
        }
        ```
        
- 상태(뮤터블한 데이터)에 대한 의존성
    - 스트림 API는 상태를 가지지 않는 순수한 함수형 프로그래밍 스타일을 장려한다. 하지만 파이프라이닝을 사용할 때, 중간 연산들이 상태에 의존한다면 예기치 않은 결과를 초래할 수 있습니다. 따라서 파이프라이닝을 사용할 때는 중간 연산들이 불변성과 상태에 대한 의존성을 잘 고려해야 한다.

### 5. 잘못된 forEach() 사용

- 스트림 패러다임의 핵심은 계산을 일련의 변환으로 재구성 하는 부분이다. 이때 각 변환 단계는 가능한 이전 단계의 결과를 받아 처리하는 **순수 함수(↔side effect 함수)**여야 한다.
- 이펙티브 자바(아이템 46)에 의하면, *forEach 연산은 최종 연산 중 기능이 가장 적고 가장 ‘덜’ 스트림답기 때문에, forEach 연산은 스트림 계산 결과를 보고할 때(주로 print 기능)만 사용하고 계산하는 데는 쓰지 말자*고 권한다.
    - 스트림 패러다임을 이해하지 못한 채 사용하는 forEach() 예시
        
        ```java
        Map<String, Long> freq = new HashMap<>();
            try(
                Stream<String> words = new Scanner(file).tokens())
                    words.forEach(word -> {
                        freq.merge(word.toLowerCase(), 1L, Long::sum);
                    });
                    }
        ```
        
    - forEach()를 제대로 이해하고 사용한  예시
        
        ```java
        Map<String, Long> freq;
            try (Stream<String> words = new Scanner(file).tokens()) {
                freq = words.collect(groupingBy(String::toLowerCase, counting()));
            }
        ```
        

### Reference

- [[Effective Java] 이펙티브자바 정리: 7장 람다와 스트림 *](https://scshim.tistory.com/513)
- [자바8 Streams API 를 다룰때 실수하기 쉬운것 10가지](https://hamait.tistory.com/547)
- [Stream](https://incheol-jung.gitbook.io/docs/q-and-a/java/stream)
- [[Java] 스트림에서 오토박싱 (박싱, 언방식)이 일어나는 이유](https://thalals.tistory.com/361)

## Q2. **스트림의 게으름(Lazy)이란 무엇이고 루프퓨전은 무엇인가?**

### 1. 지연 연산이란?

Lazy Evaluation**은 실제로 필요로 해지는 경우에 연산을 시작**하는 것이다. 이와 달리 Eager Evaluation이 있으며 이는 할당되자마다 연산을 시작한다.

- 예시
    
    ```java
    @Test
    public void solution() {
        String value = compute("Hello_1") && compute("Hello_2") ? "match" : "incompatible!";
    }
    ```
    
    - 코드는 `&&` 논리연산자로 묶여 있다.
    - 이 경우 첫번째 `compute("Hello_1")`에 대한 평가가 먼저 이루어져, 만일 true면 바로 “match”를 반환한다.

### 2. 스트림 최적화

- 스트림 파이프라인을 실행하게 되면 JVM은 곧바로 스트림 연산을 실행시키지 않는다.
- 그 대신 최소한의 필수적인 작업만을 수행하고자, 즉 지연 연산을 위한 준비작업을 수행한다.
- 이 준비 작업이란 우선 스트림 파이프라인이 어떠한 중간연산과 최종연산으로 구성되어있는지에 대한 검사로 시작된다.
- 이러한 검사 결과를 바탕으로 JVM은 사전에 어떠한 방식으로 최적화를 진행할지 미리 계획하고, 그 계획에 따라 스트림의 개별 요소에 대한 스트림 연산을 수행하게 된다.
- 그리고 스트림에서 제공하는 최적화 전략으로는 루프퓨전과 쇼트서킷이 대표적이다.

### 3. 루프퓨전이란?

루프퓨전(loop fusion)이란 파이프라인에서 연속적으로 체이닝된 복수의 스트림 연산을 하나의 연산 과정으로 병합시키는 것을 뜻한다.

- 예시
    
    ```java
    Stream.of(new Data(1), new Data(2), new Data(3)) .peek(Data::runOperationA) .peek(Data::runOperationB) .forEach(Data::runOperationC); // 데이터 1의 작업A // 데이터 1의 작업B // 데이터 1의 작업C // 데이터 2의 작업A // 데이터 2의 작업B // 데이터 2의 작업C // 데이터 3의 작업A // 데이터 3의 작업B // 데이터 3의 작업C
    
    // 데이터 1의 작업A 
    // 데이터 1의 작업B 
    // 데이터 1의 작업C 
    // 데이터 2의 작업A 
    // 데이터 2의 작업B 
    // 데이터 2의 작업C 
    // 데이터 3의 작업A 
    // 데이터 3의 작업B 
    // 데이터 3의 작업C
    ```
    
    - 두 개의 중간연산 peek과 최종연산 forEach가 하나의 과정으로 병합되었다.
- 효과
    - 이를 통해 개별 스트림 요소에 접근하는 횟수가 최소화되기 때문에 스트림 최적화 방법 중 하나로 꼽힌다.

### 4. 결론

지연 연산의 최대장점은 필요하지 않은 연산은 하지 않는것이 가능하게 된다는 점이다. 필요하지 않는 연산을 지연시켜 성능적으로 좋은 결과를 가져올 수 있다. 그리고 이러한 방식 중 하나가 루프퓨전이다.

### Reference

- [[Java] 스트림: 지연 연산과 최적화](https://bugoverdose.github.io/development/stream-lazy-evaluation/)
- [Java와 Lazy Evaluation](https://sabarada.tistory.com/153)

## Q3. **스트림에서 병렬 처리는 어떻게 이루어질까?**

### 1. 병렬 처리란?

- 한가지 작업을 서브 작업으로 나누고, 서브 작업들을 분리된 스레드에서 병렬적으로 처리한 후, 서브 작업들의 결과들을 최종 결합하는 방법이다.
- 병렬 스트림은 Fork-Join 프레임워크와 common pool of worker 스레드를 사용한다.

### 2. **Fork-Join Framework**

- 소스 분할
    - fork-join 프레임워크는 worker 스레드간에 소스 데이터를 분할하고 작업 완료시 콜백을 처리하는 역할을 합니다.
        - 예시
            - 순차 스트림에서 이 작업의 결과는 15이지만, 병렬 스트림에서는 모든 worker 스레드에서 숫자 5가 합산된다.
            
            ```java
            List<Integer> listOfNumbers = Arrays.asList(1, 2, 3, 4);
            int sum = listOfNumbers.parallelStream().reduce(5, Integer::sum);
            assertThat(sum).isNotEqualTo(15); // true
            ```
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/75f888d8-f4c4-4dd6-857b-971751107c45/Untitled.png)
            
- common thread pool
    - common 스레드풀의 스레드 수는 프로세서 코어 수와 같다. 그러나 API를 사용하면 JVM 매개변수를 전달하여 사용할 스레드수를 지정할 수 있다.
        - 스레드 수 지정 명령어
            - 이는 전역 설정이기 때문에, common thread pool을 사용하는 모든 병렬 스트림 및 기타 모든 fork-join 작업에 영향을 미친다는 점을 주의해야 한다.
        
        ```java
        -D java.util.concurrent.ForkJoinPool.common.parallelism=4
        ```
        

### 3. 스트림에서의 병렬 처리
![image](https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/997f12c6-2e8c-405d-971e-5e60376a46bf)

- parallelStreamd을 통해 내부 반복자를 사용하므로 병렬 처리가 쉽다.
- 내부 반복자는 요소들의 반복 순서를 변경하거나, 멀티 코어 CPU를 최대한 활용하기 위해 요소들을 분배시켜 병렬 작업을 할 수 있게 도와주기 때문에 하나씩 처리하는 순차적 외부 반복자보다는 효율적으로 요소를 반복시킬 수 있다.

### Reference

- https://sas-study.tistory.com/461
- [https://velog.io/@mmy789/Java-스트림과-병렬-처리-1](https://velog.io/@mmy789/Java-%EC%8A%A4%ED%8A%B8%EB%A6%BC%EA%B3%BC-%EB%B3%91%EB%A0%AC-%EC%B2%98%EB%A6%AC-1)
