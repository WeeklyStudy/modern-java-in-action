# Q. collect vs Collector vs Collectors vs Collection

### Collect

Collector를 매개 변수로 받는 스트림의 최종 연산 메서드

- 메서드
    
    ```java
    Object collect(Collector collector)
    Object collect(Supplier supplier, BiConsumer accumulator, BiConsumer combiner)
    ```
    
    - Collector 객체를 인수로 전달
    - 리듀싱 연산을 정의하는 supplier, accumulator, combiner 메서드 인수로 전달

### Collector

리듀싱 연산을 어떻게 수행할지 메서드를 정의해놓은 인터페이스 

- Collector 인터페이스는 5개의 주요 메서드를 가지고 있으며, Collector 구현체는 해당 메서드를 구현해야 함
    
    ```java
    public interface Collector<T, A, R> {
    	Supplier<A> supplier();
    	BiConsumer<A, T> accumulator();
    	BinaryOperator<A> combiner();
    	Function<A, R> finisher();
    	Set<Characteristics> characteristics();
    }
    ```
    

### Collectors

자주 사용하는 컬렉터 인스턴스를 손쉽게 생성할 수 있도록 정적 팩토리 메서드로 제공하는 클래스

- 변환 - mapping(), toList(), toMap()
- 통계 - counting(), summingInt(), averagingInt(), maxBy(), minBy()
- 문자열 연결 - joining()
- 리듀싱(범용) - reducing()
- 그룹화와 분할 - groupingBy(), partitioningBy()
- 

### Collection

- Collections : Collection Framework 의 최상위 인터페이스
- 정리
    - Collection은 Iterable 상속받는 인터페이스이고, Collections는 Object를 상속받는 클래스
    - Collections는 Collection을 리턴하거나, Collection 인터페이스에서 동작하는 메소드를 모아놓은 클래스

</br>

# Q. 컬렉터를 사용하는 것과 reduce 메서드를 사용하는 것의 차이점

### 공통점

- collect() 와 reduce() 는 스트림 요소들을 리듀스 연산하는 메서드
- 동일한 기능을 구현 가능

### 차이점

- collect
    - 결과를 누적하는 컨테이너를 바꾸도록 설계된 연산
- reduce
    - 두 값을 하나로 도출하는 불변형 연산
    - 리듀싱 연산을 병렬로 수행할 수 없음 ⇒ 매번 새로운 할당으로 인한 성능 저하

</br>

# Q. groupingBy와 partitioningBy의 유사점과 차이점은 무엇일까?

## 그룹화 & 분할

- 그룹화 : 스트림의 요소를 특정 기준으로 N 개의 그룹으로 그룹화(분할) 한다.
    - groupingBy() 사용
- 분할: 스트림 요소를 참, 거짓을 기준으로 2개의 그룹으로 그룹화(분할) 하는 것
    - partitioningBy() 사용

## 유사점

- groupingBy, partitioningBy 모두 분류 함수를 기준으로 스트림 요소를 그룹화 함
    
    ⇒ 결과 형식은 Map으로 반환
    
- partitioningBy, groupingBy는 요소를 그룹화한 후 각 그룹의 요소에 조작하는 연산을 실행 가능
- 요소를 조작하는 연산(컬렉터 등)을 이용해서 다수준으로 그룹화, 분할 가능
    
    ```java
    Collector groupingBy(Function classifier, Collector downstream)
    Collector partitioningBy(Predicate predicate, Collector downstream)
    ```
    

## 차이점

- `groupingBy()`
    - 스트림 요소를 **Function(분류 함수)를 이용해** 분류
    - 기준으로 스트림이 **N개의 그룹으로 그룹화**
    - 메서드 종류
        
        ```java
        Collector groupingBy(Function classifier)
        Collector groupingBy(Function classifier, Collector downstream)
        Collector groupingBy(Function classifier, Supplier mapFactory, Collector downstream)
        ```
        
- `partitioningBy`
    
    분할 함수 : Predicate 분류함수로 사용하는 함수
    
    - 스트림 요소를 **Predicate(분할 함수) 이용해** 분류
        
        ⇒ Prediecate는 Boolean을 반환, 키 형식은 Boolean 타입으로 참, 거짓을 가짐
        
    - 조건을 만족하는 그룹과 조건을 만족하지 않는 **2개의 그룹으로 그룹화(분할)**
    - 메서드 종류
        
        ```java
        Collector partitioningBy(Predicate predicate)
        Collector partitioningBy(Predicate predicate, Collector downstream)
        ```
        

## 다수준 분할 예제

- 이중 분할
    - 성별 분할 후 점수가 100점 넘는 그룹과 아닌 그룹으로 분할
        
        ```java
        Map<Boolean, Map<Boolean, List<Student>>> failedStudentBySex = 
        Stream.of(studentArr)
        	    .collect(Collectors.partitioningBy(Student::isMale,
        																		     Collectors.partitioningBy(student -> student.getScore() <= 70)));
        ```
        
    - 근로자 성별 분할 후 나이 분할
        
        ```java
        Map<Employee.Sex, Map<Object, List<Employee>>> ageEmployee = 
        Employee.listOfStaff()
        				.stream()
                .collect(groupingBy(Employee::getGender,
        										        groupingBy(employee -> {
                                            if (employee.getAge() <25) {
                                                return "younger";
                                            } else if (employee.getAge() >50) {
                                                return "older";
                                            } else {
                                                return "middler";
                                            }
        														})));
        ageEmployee.forEach((k,v) -> System.out.println(k + ":" + v));
        ```
        
- groupingBy(), partitioningBy() 활용
    - 학년 별 그룹화 후 성별 분할, 점수가 100점 넘는 그룹과 아닌 그룹으로 분할
    
    ```java
    Map<Integer, Map<Boolean, Map<Boolean, List<Student>>>> failedStudentBySex = 
    Stream.of(studentArr)
    	    .collect(Collectors.groupingBy(Student::getGrade),
    							 Collectors.partitioningBy(Student::isMale,
    																		     Collectors.partitioningBy(student -> student.getScore() <= 100)));
    ```
