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
- **기본형 특화 스트림**으로 변환가능한 경우, 이러한 오버헤드 문제를 해결할 수 있다.
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

### 5. 잘못된 forEach() 사용

- 스트림 패러다임의 핵심은 계산을 일련의 변환으로 재구성 하는 부분이다. 이때 각 변환 단계는 가능한 이전 단계의 결과를 받아 처리하는 **순수 함수**여야 한다.
- 이펙티브 자바(아이템 46)에 의하면, ***forEach** 연산은 최종 연산 중 기능이 가장 적고 가장 ‘덜’ 스트림답기 때문에, forEach 연산은 스트림 계산 결과를 보고할 때(**출력 및 외부 전달**)만 사용하고 계산하는 데는 쓰지 말자*고 권한다.
    - **스트림 패러다임을 이해하지 못한 예시**
      - 아래 코드는 `forEach()`를 적절하지 못하게 사용하고 있다.
      - forEach가 최종 연산임에도 불구하고, 중간 연산과 최종 연산 기능이 혼합되어 있다.
      - 또한, forEach 내부에서 외부 상태(freq)를 수정하므로 **순수함수**의 성질을 충족시키지 못하고 있다.
      
        
        ```java
        Map<String, Long> freq = new HashMap<>();
            try(
                Stream<String> words = new Scanner(file).tokens())
                    words.forEach(word -> {
                        freq.merge(word.toLowerCase(), 1L, Long::sum);
                    });
                    }
        ```
        
    - **스트림을 제대로 이해한 예시**
      - 아래 코드는 forEach 대신 `collect()`를 적절하게 사용하고 있다.
      - collect는 스트림 요소를 수집하여 다양한 형태의 컬렉션을 생성하거나 결과를 집계하는 역할을 수행하는 최종 메서드이기 때문이다.
      - 또한, 외부 상태 변경이 없고, 동일한 입력에 대해 동일한 출력을 반환하여 **순수함수** 성질을 만족한다.
         - try 블록 내에서 freq 맵을 생성하고 스트림 연산을 통해 초기화한 후, try 블록이 종료되면 freq 맵에 대한 참조가 사라지고 가비지 컬렉터에 의해 처리된다. 따라서 **외부 상태 변경**이 없다.
         - **동일한 입력**인 파일의 내용에 대해 **항상 동일한 출력**인 freq 맵을 반환한다.
      - 따라서 해당 코드에서는 최종연산으로 forEach보단 collect가 권장된다.
        
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

Lazy Evaluation**은 실제로 필요해지는 경우에 연산을 시작**하는 것이다. 이와 달리 Eager Evaluation이 있으며 이는 할당되자마다 연산을 시작한다.

- 예시
    
    ```java
    @Test
    public void solution() {
        String value = compute("Hello_1") && compute("Hello_2") ? "match" : "incompatible!";
    }
    ```
    
    - 위 코드는 `&&` 논리연산자와 삼항 연산자을 활용하여 작업을 지연시키고 있다.
    - 첫 번째 연산인 `compute("Hello_1")`이 `false`인 경우, `compute("Hello_2")`는 평가되지 않고 바로 종료된다.
    - 즉, 필요한 연산만을 평가하여 실행을 최적화하고 있는 것이다.
### 2. 스트림 최적화

- 스트림 파이프라인을 실행하게 되면 JVM은 곧바로 스트림 연산을 실행시키지 않는다.
- 그 대신 최소한의 필수적인 작업만을 수행하고자, 즉 **지연 연산을 위한 준비작업**을 수행한다.
- 이 준비 작업이란 우선 스트림 파이프라인이 **어떠한 중간연산과 최종연산으로 구성되어있는지에 대한 검사**로 시작된다.
- 이러한 검사 결과를 바탕으로 JVM은 사전에 **어떠한 방식으로 최적화를 진행할지 미리 계획**하고, 그 계획에 따라 스트림의 개별 요소에 대한 스트림 연산을 수행하게 된다.
- 그리고 스트림에서 제공하는 최적화 전략으로는 루프퓨전과 쇼트서킷이 대표적이다.

### 3. 루프퓨전이란?

루프퓨전(loop fusion)이란 파이프라인에서 연속적으로 체이닝된 복수의 스트림 연산을 **하나의 연산 과정으로 병합**시키는 것을 의미한다.

- 예시
    
    ```java
    Stream.of(new Data(1), new Data(2), new Data(3))
    .peek(Data::runOperationA)
    .peek(Data::runOperationB)
    .forEach(Data::runOperationC);
    ```
    - 두 개의 중간연산 peek과 최종연산 forEach가 하나의 과정(작업 A, B, C)으로 병합된다.
    ```java
    List source = List.of(new Data(1), new Data(2), new Data(3)); 
    for (Data data : source) { 
    data.runOperationA(); 
    data.runOperationB(); 
    data.runOperationC(); 
    }
    ```
    - 마치 위 코드처럼 작업 A, B, C는 하나의 루프문으로 묶여 처리되고 있다고 이해하면 더 쉽게 이해할 수 있을 것이다.
- 효과
    - 이를 통해 개별 스트림 요소에 접근하는 횟수가 최소화되기 때문에 스트림 최적화 방법 중 하나로 꼽힌다.
      - 루프퓨전이 적용된 경우 작업 횟수 : 총 3회
         - Data(1) 객체에 대한 작업: operationA, operationB, operationC: 한 번에 1회
         - Data(2) 객체에 대한 작업: operationA, operationB, operationC: 한 번에 1회
         - Data(3) 객체에 대한 작업: operationA, operationB, operationC: 한 번에 1회
      - 루프퓨전이 적용되지 않았을 경우 작업 횟수 : 총 9회
         - Data(1) 객체에 대한 작업: operationA 1회, operationB 1회, operationC 1회
         - Data(2) 객체에 대한 작업: operationA 1회, operationB 1회, operationC 1회
         - Data(3) 객체에 대한 작업: operationA 1회, operationB 1회, operationC 1회

### 4. 결론

- 지연 연산의 최대장점은 필요한 연산만 수행하여 성능을 개선시킬 수 있다는 것이다.
- 루프 퓨전은 스트림 연산의 최적화 기법으로서 지연연산을 활용하여 중간 작업들을 효율적으로 결합시키는 개념이다. 중간 결과를 생성하지 않고 한 번에 처리하여 성능을 향상시킬 수 있다.

### Reference

- [[Java] 스트림: 지연 연산과 최적화](https://bugoverdose.github.io/development/stream-lazy-evaluation/)
- [Java와 Lazy Evaluation](https://sabarada.tistory.com/153)

## Q3. 스트림에서 병렬 처리는 어떻게 이루어질까?

### 1. 병렬 처리
- 병렬 처리는 작업을 여러 개의 **서브 작업**으로 분할하고, 각각의 서브 작업을 병렬적으로 처리하여 성능을 향상시키는 방법이다.
- 최신 컴퓨터들은 모두 **멀티코어**를 장착하여 병렬을 더욱 잘 활용할 수 있는 환경을 갖추게 되었다.
- 다만, 멀티코어 환경에서는 컴퓨터가 알아서 병렬처리를 해 주는 것이 아니라, 프로그램을 구현할 때 동시에 수행될 수 있는 대상을 찾아 Thread를 만드는 작업, Thread 간의 자원경쟁을 해소하기 위한 동기화 작업 등을 반드시 수행해주어야 한다.
  ![image](https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/d3255bab-e383-4168-881d-6f458667ca1a)


#### 💡 멀티코어
> - 병렬컴퓨터는 결합 구조에 따라 분류할 수 있는데, 다수의 프로세서가 주기억 장치 및 I/O를 공유하며, 하나의 운영체제가 모든 프로세서를 관리하는 구조(Symmetric Multi-Processor)의 한 예가 멀티 코어다.
> - 싱글코어에서는 프로세서가 한 개밖에 없기 때문에 운영체제 수준에서 멀티태스킹 기법을 제공한다. CPU를 짧게 번갈아 가며 점유하는 것이다.
> - 이와 달리, 멀티 코어는 여러 개의 작업을 각 코어에 나누어 분배하여 병렬 처리가 가능하다.

### 2. 스트림의 병렬 처리
- 스트림 API에서는 parallel() 메서드를 호출하여 병렬 스트림을 생성할 수 있다.
- parallel() 메서드를 호출하면 스트림은 병렬 처리를 위해 내부적으로 스레드 풀을 사용한다.
- 스트림의 작업은 여러 개의 작은 서브 작업으로 분할되고, 스레드 풀의 여러 스레드에서 동시에 병렬적으로 처리된다.
- 각각의 스레드는 분할된 작업을 독립적으로 실행하고, 작업이 완료되면 그 결과를 결합하여 최종 결과를 얻을 수 있다.

## 3. Fork-Join Framework와 Common Pool of Worker Threads
- **Fork-Join Framework**
    - Fork-Join Framework는 자바의 ForkJoinPool 클래스를 기반으로 동작한다. 
    - 이 프레임워크는 작업을 작은 작업으로 분할하고, 분할된 작업을 병렬적으로 실행하며, 그 결과를 결합하는 방식으로 동작한다.
        - 예시
            - 순차 스트림에서 이 작업의 결과는 15이지만, 병렬 스트림에서는 모든 worker 스레드에서 숫자 5가 합산된다.
            
            ```java
            List<Integer> listOfNumbers = Arrays.asList(1, 2, 3, 4);
            int sum = listOfNumbers.parallelStream().reduce(5, Integer::sum);
            assertThat(sum).isNotEqualTo(15); // true
            ```
            <img src="https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/48ed116e-d9c9-4b48-aaf5-768176764b2b" width="400">

            
- **Common Pool of Worker Threads**
    -  Fork-Join Framework를 위해 미리 생성된 스레드 풀이다.
    -  기본적으로는 프로세서 코어 수에 맞춰 스레드가 생성되지만, JVM 매개변수를 통해 스레드수를 지정할 수 있다.
        - 스레드 수 지정 명령어
          - 이는 전역 설정이기 때문에, Common Thread Pool을 사용하는 모든 병렬 스트림 및 기타 모든 Fork-Join 작업에 영향을 미친다.
            ```java
            -D java.util.concurrent.ForkJoinPool.common.parallelism=4
            ```

### 4. 결론
- 스트림의 병렬 처리는 내부 반복자를 통해 이루어진다.
- 내부 반복자는 요소들을 병렬적으로 처리하기 위해 요소들의 반복 순서를 변경하거나 분배하여 멀티 코어 CPU를 최대한 활용한다.
- 이를 통해 개발자는 요소들을 하나씩 처리하는 외부 반복자를 직접 작성할 필요가 없어졌을 뿐만 아니라, 더욱 효율적으로 병렬로 처리할 수 있게 되었다.

### Reference

- [자바에서 병렬 스트림을 사용하는 경우](https://sas-study.tistory.com/461)
- [https://velog.io/@mmy789/Java-스트림과-병렬-처리-1](https://velog.io/@mmy789/Java-%EC%8A%A4%ED%8A%B8%EB%A6%BC%EA%B3%BC-%EB%B3%91%EB%A0%AC-%EC%B2%98%EB%A6%AC-1)
- [멀티코어와 병렬](https://velog.io/@injoon2019/%EB%B3%91%EB%A0%AC%ED%99%94%EC%99%80-%EC%9E%A5%EB%8B%A8%EC%A0%90%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC)
