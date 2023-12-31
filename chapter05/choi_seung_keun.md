# Q. findFirst()와 findAny()의 차이점은 무엇이고 언제 사용하는 것이 좋을까?

### 공통점

- Stream에서 조건에 일치하는 요소 1개를 Optional로 리턴
- 일치하는 요소 없다면 empty 리턴

### 차이점

- findFirst()는 조건에 일치하는 요소 중 가장 앞에 있는 요소를 반환하고, findAny()는 가장 먼저 탐색되는 임의의 요소를 반환한다.
    
    ⇒ 요소의 순서의 차이를 가지고 있음
    
    ⇒ 병렬처리에서 순서는 다른 결과를 만듦
    

### 사용하는 시점

반환하는 요소의 순서의 차이를 가지고 있기에 다르게 사용

- 병렬 처리에서 첫 번째 요소 검색 어려움
- 병렬 처리를 사용할 때 순서에 상관없이 가장 먼저 탐색된 요소를 반환하는 findAny() 사용

</br>

# Q. flatMap 이란 무엇이고, 어떤 상황에서 사용하면 좋을까?

### flatMap 메서드

- 스트림의 형태가 배열과 같을 때 모든 요소를 단일 요소 스트림으로 반환
    
    ```java
    String[][] namesArray = new String[][]{
            {"kim", "taeng"}, {"mad", "play"},
            {"kim", "mad"}, {"taeng", "play"}};
            
    Set<String> namesWithFlatMap = Arrays.stream(namesArray)
            .flatMap(innerArray -> Arrays.stream(innerArray))
            .filter(name -> name.length() > 3)
            .collect(Collectors.toSet());
            
    // play, taeng 출력
    namesWithFlatMap.forEach(System.out::println);
    ```
    
- 사용하면 좋은 경우
    
    스트림의 형태가 배열인 경우 또는 입력된 값을 또 다시 스트림의 형태로 반환하고자 할 때 유용

</br>

# Q. 상태있음과 상태없음은 병렬 처리에서 어떤 연관성이 있을까?
### 상태가 없는 연산

현재 순회 중인 요소를 처리하는 동안 이전에 처리된 데이터 요소의 상태를 유지하지 않는 연산

- 각 요소의 처리는 다른 요소와 독립되어 수행 ⇒ 성능상의 이점
- 상태가 없는 중간 연산으로만 이뤄진 파이프 라인의 경우 데이터 요소에 한 번만 접근
- filter, map, takeWhile 등 연산이 있다.

### 상태가 있는 연산

현재 순회 중인 요소를 처리하는 동안 이전에 처리된 데이터 요소의 상태를 활용하는 연산

- 각 연산의 특징에 따라 필요로 하는 요소를 내부 상태로 저장
- 병렬 처리 환경에서 **상태를 가진 연산을 포함하는 파이프라인은 같은 요소를 여러 번 접근하거나 주요 데이터를 별도로 관리** 해야 할 수 있다.
- 관리되어야 하는 상태의 크기에 따라 bound, unbound로 분류
- sorted, distinct, reduce, skip, limit 등 연산이 있다.
    
    
1. 한정된 상태가 있는 연산(Bound)
    
    요소의 개수와 상관없이, 수행되기 위해 관리되어야 하는 상태의 크기가 한정되는 연산
    
    - 예시
        - min 연산은 여태 작업을 통해 가장 작은 요소를 상태로 가지고 있음
        - limit 은 현재까지 순회한 데이터 요소 개수 만큼 상태로 가지고 있음
        - reduce는 현재까지 순회한 데이터 결과를 상태로 가지고 있음
    
2. 한정되지 않은 상태가 있는 연산(Unbound)
    
    스트림 내의 요소에 따라 관리되어야 하는 상태의 크기가 변하는 연산
    
    - 스트림의 모든 요소를 버퍼에 가지고 있어야 연산 가능 ⇒ 데이터 크기가 무한인 상태(무한스트림)에서 문제를 일으킬 수 있음
    - 예시
        - sorted 연산은 정렬을 하기 위해 모든 요소를 상태로 가지고 있어야 함
        - distinct 연산은 이전에 순회한 데이터와 중복되는지 확인을 위해 순회한 데이터 목록을 가지고 있어야 함

### 상태있는 연산과 병렬 처리의 연관성

- 병렬 처리 특징
    - 병렬 처리이기 때문에 순서를 보장하지 않음
    - parallel()은 공유된 스레드 풀을 사용 ⇒ 심각한 성능 장애를 일으킬 수 있음
    - 분할과 병합의 과정에서 발생하는 오버헤드가 존재
- 병렬 처리 환경에서 **상태를 가진 연산을 포함하는 파이프라인은 같은 요소를 여러 번 접근하거나 주요 데이터를 별도로 관리(버퍼링)** 해야 할 수 있다.
- 결론
    - 상태가 있는 연산은 병렬 처리 시 수행 성능의 영향을 줄 수 있다.
    - 병렬 처리는 독립적인 연산 작업을 할 때 사용하는 것이 좋으며 성능 최적화, 장애 여부를 충분히 검증하여 적용해야 한다.
