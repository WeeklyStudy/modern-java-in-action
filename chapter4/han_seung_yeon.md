# https://github.com/WeeklyStudy/modern-java-in-action/issues/10 스트림의 게으름(Lazy)이란 무엇이고 루프퓨전은 무엇인가?
    
## 💡Eager Evaluation(조급한 연산)

> 변수에 접근하면 곧바로 연산을 시작하는 기법이다.
> 

## 💡Lazy Evaluation(지연 연산)

> 결과값이 필요할 때까지 계산을 늦추는 기법이다.
> 
- 불필요한 연산은 수행하지 않기 때문에 대부분 성능이 좋다.
- 다만, **지연 연산을 위한 내부 준비 작업**을 수행하기 때문에 무조건 효율적인 방식은 아니다.

## 💡스트림의 지연 연산과 최적화

1. 스트림 파이프라인이 실행된다.
2. **전체 파이프라인 구성(어떤 중간 연산, 최종 연산으로 구성되었는지)를 검사**한다.
3. 검사 결과를 바탕으로 JVM은 어떤 방식으로 최적화를 진행할지 계획한다.
4. 계획에 따라 **최종 연산이 호출되는 시점에 스트림 연산을 수행**한다.

## 💡루프 퓨전(loop fusion)이란?

> 파이프라인에서 **복수의 스트림 연산을 하나의 연산 과정으로 병합**하는 것이다.
> 
- 루프 퓨전은 **스트림 최적화 전략**의 일종이다.
    - 복수의 스트림 연산이 하나로 병합되면, **개별 스트림 요소에 접근하는 횟수가 최소화**된다.
- 다만, **모든 스트림 연산에 대해 루프 퓨전이 적용되는 것은 아니다!**
    - 정렬 작업을 수행하기 위해서는 모든 요소가 필요하기 때문에 이러한 경우에는 루프 퓨전이 적용되지 않을 수 있다.

## 💡쇼트 서킷(short circuit)이란?

> **불필요한 연산을 수행하지 않음**으로써 실행 속도를 높이는 기법이다.
> 
- 스트림 `limit` 같은 연산을 활용해 스트림 일부 요소들에 대한 연산을 완전히 생략한다.

## 💡Eager Evaluation 과 Lazy Evaluation 비교

1~10 이 담긴 리스트에서 1) 6보다 작고 2) 짝수인 요소에 3) 10을 곱하여 4) 리스트로 만드는 예제다.

### 1. Eager Evaluation

> Eager Evaluation 방식의 반복문 예제
> 

```java
public static void t1() {
    List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
    int result = 0;
    // 1번
    List<Integer> list1 = new ArrayList<>();
    for (Integer i : list) {
        System.out.println(i + ": i < 6");
        if(i < 6) {
            list1.add(i);
        }
    }
    // 2번
    List<Integer> list2 = new ArrayList<>();
    for (Integer i : list1) {
        System.out.println(i + ": i % 2 == 0");
        if(i % 2 == 0) {
            list2.add(i);
        }
    }
    // 3번
    for (Integer i : list2) {
        System.out.println(i + ": i *= 10");
        i *= 10;
        result = i;
        break;
    }
    System.out.println(result);
}
```

1. 전체 요소를 대상으로 6보다 작은지에 대한 검사를 반복한다.
2. 1번에서 구한 요소를 대상으로 짝수인지에 대한 검사를 반복한다.
3. 2번에서 구한 요소를 대상으로 10을 곱하는 것을 반복한다.

⇒ 한 요소에 3번 접근한다. 1, 2, 3 과정이 구분되어 있다.

<img width="143" alt="Untitled (2)" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/3ee9e765-0958-4a36-b484-1c3fdf4e99e0">

### 2. Lazy Evaluation

> Lazy Evaluation 방식의 Stream 예제
> 

```java
public static void t2() {
    final List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
    int result = list.stream()
            .filter(i -> {
                System.out.println(i + ": i < 6");
                return i < 6;
            })
            .filter(i -> {
                System.out.println(i + ": i % 2 == 0");
                return i % 2 == 0;
            })
            .map(i -> {
                System.out.println(i + ": i *= 10");
                return i * 10;
            })
            .findFirst()
            .get();
    System.out.println("================");
    System.out.println(result);
}
```

1. 한 요소가 6보다 작은지 검사한다.
2. 1번을 만족한다면 짝수인지 검사한다.
3. 2번을 만족한다면 10을 곱한다.
4. 3번에서 값을 구하면 나머지 요소에 대한 연산은 하지 않는다.

⇒ 한 요소에 1번 접근한다. 1, 2, 3 과정이 하나로 묶였다.

<img width="140" alt="Untitled (3)" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/fc4f65f9-2f55-423e-8f8c-c674b8c7d08f">

> 같은 동작을 하는 반복문
> 

```java
public static void t3() {
    List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
    int result = 0;
    // 1번
    for (Integer i : list) {
        System.out.println(i + ": i < 6");
        if(i<6) {
            System.out.println(i + ": i % 2 == 0");
            if(i%2==0) {
                System.out.println(i + ": i *= 10");
                i *= 10;
                result = i;
                break;
            }
        }
    }
    System.out.println("================");
    System.out.println(result);
}
```

## 💡결론

- 자바8의 `스트림`은 `Lazy Evaluation` 방식이다.
- `Lazy Evaluation` 의 최대 장점은 필요하지 않은 연산은 하지 않아도 된다는 점이다.
- 스트림에서는 `지연 연산`을 기반으로 `루프 퓨전`과 `쇼트서킷`의 최적화 전략을 사용한다.
- 스트림에서 `중간 연산`은 즉시 실행되지 않고 `최종 연산`이 호출될 때 뒤늦게 실행된다.

## Reference

- [[Java] 스트림: 지연 연산과 최적화*](https://bugoverdose.github.io/development/stream-lazy-evaluation/)
- [[자바] Lazy Evaluation 이란?*](https://dororongju.tistory.com/137)
- [[10분 테코톡] 차리의 Stream](https://www.youtube.com/watch?v=rbm87IFpwvQ)
- [Lazy Evaluation과 메모리](https://medium.com/@goinhacker/lazy-evaluation%EA%B3%BC-%EB%A9%94%EB%AA%A8%EB%A6%AC-c6789ac2173c)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/11 스트림에서 병렬 처리는 어떻게 이루어질까?

## 💡병렬 처리(Parallel Operation)

> 병렬 처리란 멀티 코어 CPU 환경에서 하나의 작업을 분할해 각각의 코어가 병렬적으로 처리(동시에 실행)하는 것이다.
> 
- `병렬 스트림`은 `데이터 병렬성`을 구현한 것이다.
- `병렬 스트림`은 내부적으로 `ForkJoinPool` 을 사용한다.

> ✏️ 용어 정리
> 
> - `병렬 스트림` : 각각의 스레드에서 처리할 수 있도록 **스트림 요소를 여러 청크로 분할한 스트림**
> - `데이터 병렬성` : 한 작업 내에 있는 전체 데이터를 쪼개서 서브 데이터로 만들고 서브 데이터들을 병렬 처리하는 것

## 💡포크/조인 프레임워크
<img src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/dcd11cc6-0599-48fe-9d9c-4f3fbeca6a06" width="400" height="300"/>

### 1. 포크(Fork) 단계

- 데이터를 서브 데이터로 분리한다.(실제로 차례대로 요소를 n등분 하지는 않고 내부 알고리즘을 사용한다.)
- 서브 데이터를 멀티 코어에서 병렬로 처리한다.

### 2. 조인(JOIN) 단계

- 서브 결과를 결합해서 최종 결과를 만들어 낸다.

## Reference

- [[JAVA] Stream, 스트림과 병렬처리](https://ict-nroo.tistory.com/43)
- [[Java] 병렬 데이터 처리(병렬 스트림, 포크/조인 프레임워크)](https://girawhale.tistory.com/131)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/12 스트림 사용 시 주의할 점에는 무엇이 있을까?
    
## 💡무한 스트림 생성 문제
- 스트림을 사용할 때 상황에 따라 `루프 퓨전`이 적용되지 않아 의도치않게 무한 스트림이 생성되는 문제가 생길 수 있다.
- 중복을 제거하는 `distinct()` 나 전체 데이터를 정렬하는 `sorted()` 같은 `stateful 연산`은 지연 연산을 무효화시킨다.
- `stateful 연산` 을 수행하기 위해서는 스트림을 구성하는 모든 요소가 필요하기 때문이다.

> ✏️ 용어 정리
> 
> - `무한 스트림` : 크기가 정해지지 않은 스트림
> - `stateful 연산` : 이전 상태를 참조하지 않는 연산

### 1. 무한 스트림 생성 문제

- `distinct()` 는 `iterate()` 로부터 무한한 값을 받아들이고 `limit(10)` 에 도달하지 못한다.
- 서버가 다운될 때까지 `iterate()` 가 실행된다.

```java
// 무한 스트림 생성 문제
public static void t1() {
      IntStream.iterate(0, n -> (n + 1) % 2)
            .distinct()
            .limit(10)
            .forEach(System.out::println);
}
```

- [distinct 와 sorted 의 무한 스트림 생성 예제 실행 결과에 대한 의문점](https://github.com/WeeklyStudy/modern-java-in-action/issues/12#issuecomment-1632432817)

### 2. 무한 스트림 생성 문제 해결

- `iterate()`로 부터 생성된 무한 스트림에 대해 `limit(10)`으로 10개의 데이터를 고르고 `distinct()` 로 중복을 제거한다.

```java
// 무한 스트림 생성 문제 해결
public static void t2() {
    IntStream.iterate(0, n -> (n + 1) % 2)
            .limit(10)
            .distinct()
            .forEach(System.out::println);
}
```

## 💡결론

- `stateful 연산`은 `지연 연산`을 무효화시킨다.
- `stateful 연산`과 `limit` 연산의 잘못된 순서로 인해 의도치 않게 `무한 스트림`이 생성될 수 있다.
- `무한 스트림`을 생성하는 `iterate()` 와 `generate()` 는 보통 `limit()`와 함께 사용한다.

## Reference

- 스트림 주의할 점
    - [[Java] 스트림: 지연 연산과 최적화*](https://bugoverdose.github.io/development/stream-lazy-evaluation/)
    - [자바8 Streams API 를 다룰때 실수하기 쉬운것 10가지](https://hamait.tistory.com/547)
    - [[Effective Java] 아이템 45 : 스트림은 주의해서 사용하라*](https://velog.io/@semi-cloud/Effective-Java-%EC%95%84%EC%9D%B4%ED%85%9C-45-%EC%8A%A4%ED%8A%B8%EB%A6%BC%EC%9D%80-%EC%A3%BC%EC%9D%98%ED%95%B4%EC%84%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)
- stateful 연산
    - [[Java8] Stream API](https://jjingho.tistory.com/146)
