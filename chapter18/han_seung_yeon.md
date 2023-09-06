# https://github.com/WeeklyStudy/modern-java-in-action/issues/36 불변 객체란? 

## 💡불변 객체(Immutable Object)란?

> 객체 생성 이후 내부의 상태가 변하지 않는 객체이다.
> 
- 객체에 값을 할당하면 내부 데이터를 변경시킬 수 없다.

```java
// 793331940
String s = "a";
System.out.println(System.identityHashCode(s));

// 522553046
s = "b";
System.out.println(System.identityHashCode(s));
```

## 💡객체의 필드 타입이 원시 타입인 경우

***필드에 `final` 키워드를 사용하고 `setter` 를 구현하지 않는다.***

```java
public class Age {
    private final int value;

    public Age(final int value) {
        this.value = value;
    }
}
```

## 💡객체의 필드 타입이 참조 타입인 경우

참조 타입이 있는 대부분의 경우는 필드에 `final` 키워드를 사용하고 `setter` 를 구현하지 않는 것만으로는 불변 객체를 만들 수 없고 추가적인 작업이 필요하다.

### 1. 일반 객체를 참조하는 경우

필드에 `final` 키워드를 사용하고 `setter` 를 구현하지 않는다.

***+참조 변수도 불변 객체로 만든다.***

```java
public class Animal {
    private final Age age;

    public Animal(final Age age) {
        this.age = age;
    }
}

class Age {
    private final int value;

    public Age(final int value) {
        this.value = value;
    }
}
```

### 2. 배열을 참조하는 경우

필드에 `final` 키워드를 사용하고 `setter` 를 구현하지 않는다.

+***생성자의 인자로 받은 배열을 방어적 복사해서 저장하고, getter에서 clone을 반환하도록 구현한다.***

```java
public class Animal {
    private final int[] array;

    public ArrayObject(final int[] array) {
        this.array = Arrays.copyOf(array,array.length);
    }

    public int[] getArray() {
        return (array == null) ? null : array.clone();
    }
}
```

+***만약 원시 타입 배열이 아닌, 객체 배열이라면 해당 객체 또한 불변 객체로 만든다.***

```java
public class Animal {
    private final Ability[] array; // Ability가 불변 객체여야 함
    ...
}
```

### 3. 리스트를 참조하는 경우

필드에 `final` 키워드를 사용하고 `setter` 를 구현하지 않는다.

***+생성자의 인자로 받은 리스트를 방어적 복사해서 저장하고, getter에서 `Collections.unmodifiableList(리스트)` 를 반환하도록 구현한다.***

```java
public class ListObject {
    private final List<Animal> animals;

    public ListObject(final List<Animal> animals) {
        this.animals = new ArrayList<>(animals);
    }

    public List<Animal> getAnimals() {
        return Collections.unmodifiableList(animals);
    }
}
```

> **📍얕은 복사 vs 방어적 복사 vs 깊은 복사**
> 
> - `얕은 복사` : 객체의 주소값만 복사하는 방법
> - `방어적 복사` : 객체의 내부 값을 참조하여 복사하는 방법
> - `깊은 복사` : 객체의 모든 내부 상태를 완전히 복사하여 새로운 객체를 만드는 방법

## 💡결론

- 불변 객체는 한 번 할당하면 필드 값을 변경할 수 없는 객체다. but, 재할당은 가능하다.
- 필드가 원시타입인 경우 `final` 을 사용하고 `setter` 를 정의하지 않음으로써 불변 객체를 만들 수 있다. but, 필드가 참조 타입인 경우에는 추가 작업이 필요하다.

## Reference

- 불변 객체
    - [[Java] Immutable Object(불변객체)*](https://velog.io/@conatuseus/Java-Immutable-Object%EB%B6%88%EB%B3%80%EA%B0%9D%EC%B2%B4)
    - [신입 개발자 기술면접 질문 정리 - 자바](https://dev-coco.tistory.com/153)
    - [불변 객체란? Java Immutable Object](https://velog.io/@kyle/%EB%B6%88%EB%B3%80-%EA%B0%9D%EC%B2%B4%EB%9E%80-Java-Immutable-Object)
- 얕은 복사, 방어적 복사, 깊은 복사
    - [[Java] 얕은 복사, 방어적 복사, 깊은 복사](https://ttl-blog.tistory.com/1206)
    - [Collection의 깊은 복사와 방어적 복사*](https://alexander96.tistory.com/16)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/37 반복문과 재귀 방식의 차이점은 무엇이고 꼬리 재귀란 무엇인가?

## 💡반목문 vs 재귀 함수

### 1. 반복문과 재귀함수의 차이점

|  | 반복문 | 재귀함수 |
| --- | --- | --- |
| 정의 | 명령을 반복적으로 실행 | 함수를 다시 호출하여 반복 작업 수행 |
| 스택 메모리 | 스택 메모리 사용X | 함수 호출 시 함수의 매개변수, 지역변수, 리턴값, 함수 종료 후 돌아갈 위치를 스택 메모리에 저장 |
| 무한반복 | 조건식이 계속 참이면 무한 루프 발생 → CPU 사이클을 반복적으로 사용 | 종료 조건에 수렴하지 않으면 무한 재귀 발생 → 스택 오버플로우 발생 |
| 속도 | 빠른 실행 | 느린 실행 |
| 가독성 | 코드 길이가 길고 변수가 많아 가독성이 낮음 | 코드 길이 짧고 변수가 적어 가독성이 높음 |

> 📍**스택 오버플로우(Stack Overflow)**
> 
> 
> 함수가 호출될 때마다 매개변수, 지역변수, 리턴값, 함수 종료 후 돌아갈 위치 등의 정보가 스택 메모리에 저장된다.
> 
> 재귀함수는 함수의 실행이 끝나기 전에 함수를 연속적으로 호출하므로 스택 메모리를 초과하면 스택 오버플로우가 발생한다.
> 
> 반목문은 지역변수가 호출될 때 한 번만 스택 메모리에 할당되기 때문에 무한 루프가 발생하더라도 스택 오버플로우가 발생하지 않는다.
> 

### 2. 재귀함수의 장단점

### 2.1. 장점

1. 코드가 짧아지고 가독성이 높아진다.
2. 변수 사용을 줄여준다.
    - 반복문에서 사용하는 반복 변수같은 mutable state(변경 가능한 상태)를 제거하여 프로그램 오류 발생 가능성을 줄인다.

### 2.2. 단점

1. **반복적 함수 호출로 인해 반복문에 비해 메모리 사용량이 높고 속도가 느리다.**
2. 함수 호출 후 복귀를 위한 컨텍스트 스위칭 비용이 발생한다.

> 📍**컨텍스트 스위칭(Context Switching)**
> 
> 
> 여러 개의 프로세스가 실행되고 있을 때 기존에 실행되던 프로세스르 중단하고 다른 프로세스를 실행하는 것
> 

## 💡꼬리 재귀(Tail Recursion)

재귀함수의 메모리 성능에 대한 단점을 보완하기 위한 방법이 바로 꼬리 재귀다.

> 재귀 호출이 끝나면 아무 일도 하지 않고 결과만 바로 반환되도록 하는 방법
> 

### 1. 꼬리 재귀 최적화를 위한 2가지 조건

2가지 조건을 만족한다면 컴파일러는 꼬리 재귀 최적화를 지원해 자체적으로 재귀함수를 반복문으로 변경해서 실행한다.

1. 재귀 함수를 꼬리 재귀 방식으로 구현할 것
    - return 문에 추가적인 연산이 없도록 구현해야 함
2. 컴파일러가 꼬리 재귀 최적화를 지원할 것
    - 컴파일러가 꼬리 재귀 최적화를 지원하지 않는다면 꼬리 재귀 방식으로 구현해도 최적화X

### 2. 자바의 꼬리 재귀 최적화

자바 컴파일러는 꼬리 재귀 최적화를 지원하지 않는다.

하지만, 자바 8에서 반복을 스트림으로 대체해 변화를 피할 수 있다.

## 💡factorial 예제를 통한 반복문, 재귀, 꼬리 재귀 방식의 비교

### 1. 반복문

> 명령을 반복적으로 실행하는 것
> 

```java
// 반복문
public int factorialIterative(int n){
    int res = 1;
    for(int i = 1 ; i <= n ; i++){
        res *= i;
    }
    return res;
}

@Test
@DisplayName("[반복문]")
void t1() {
    factorialIterative(5);
}
```

- 매개변수, 지역변수가 스택 메모리에 할당된다.

### 2. 재귀

> 한 함수가 자신을 다시 호출하여 반복 작업을 수행하는 것
> 

```java
// 일반 재귀
public int factorialRecursive(int n){
    if(n == 1) return 1;
    return n * factorialRecursive(n - 1);
}

@Test
@DisplayName("[재귀 ]")
void t2() {
    factorialRecursive(5);
}
```

- 호출당한 함수의 결과값에 추가 연산한 값을 반환한다.
    - 호출한 함수 : `factorialRecursive(n)`
    - 호출당한 함수 : `factorialRecursive(n - 1)`
    - 호출한 함수의 결과값 : `n * factorialRecursive(n - 1)`
- 재귀 호출 이후 추가 연산을 하기 때문에, 함수 종료 후 돌아갈 위치를 스택 메모리에 저장해야 한다.
<img width="400" alt="img1 daumcdn (1)" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/f1939888-d4c2-4439-8d9d-03a7a960cee6">
<img width="400" alt="img1 daumcdn (1)" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/a2dcb8ae-2df5-43e8-b8e8-f6b721bc590e">

### 3. 꼬리 재귀

```java
// 꼬리 재귀
// acc : 연산 결과값을 전달하기 위한 매개변수
public int factorialTailRecursive(int n, int acc){
    if(n == 1) return acc;
    return factorialTailRecursive(n - 1, n * acc);
}

@Test
@DisplayName("[꼬리 재귀]")
void t3() {
    factorialTailRecursive(5, 1);
}
```

- 호출 당한 함수의 결과값이 호출하는 함수의 결과값으로 반환된다.
    - 호출한 함수 : `factorialRecursive(n, acc)`
    - 호출당한 함수 : `factorialRecursive(n - 1, n * acc)`
    - 호출한 함수의 결과값 : `factorialRecursive(n - 1, n * acc)`
    - 즉, 가장 마지막으로 계산된 `acc` 값이 그대로 반환된다.
- 재귀 호출 이후 추가 연산을 하지 않기 때문에, 함수 종료 후 돌아갈 위치를 스택 메모리에 저장할 필요가 없다.

<img width="400" alt="img1 daumcdn (3)" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/1cfd4c29-937a-43a8-a738-b4554a1ac4b2">
<img width="400" alt="img1 daumcdn (3)" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/c6a27ead-db6c-4c97-bd92-bdb0bcd88a16">


## Reference

- 반복문 vs 재귀
    - [반복문(iteration) vs 재귀(recursion)*](https://tecoble.techcourse.co.kr/post/2020-04-30-iteration_vs_recursion/)
    - [재귀함수의 장점과 단점 그리고 해결책](https://catsbi.oopy.io/dbcc8c79-4600-4655-b2e2-b76eb7309e60)
    - [[Algorithm] 반복문(iteration) vs 재귀(recursion)](https://yeonjewon.tistory.com/80)
- 꼬리 재귀
    - [꼬리 재귀 (Tail Recursion)*](https://joooing.tistory.com/entry/%EC%9E%AC%EA%B7%80-%E2%86%92-%EA%BC%AC%EB%A6%AC-%EC%9E%AC%EA%B7%80-Tail-Recursion)
    - [재귀함수와 꼬리 재귀](https://velog.io/@dldhk97/%EC%9E%AC%EA%B7%80%ED%95%A8%EC%88%98%EC%99%80-%EA%BC%AC%EB%A6%AC-%EC%9E%AC%EA%B7%80)
    - [[Play with Java] Java 8로 꼬리재귀 함수 만들기 (Tail Recursion)](https://loosie.tistory.com/790#Java%EC%97%90%EC%84%9C_%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%90%9C_%EC%9D%BC%EB%B0%98_%EC%9E%AC%EA%B7%80%EC%99%80_%EA%BC%AC%EB%A6%AC_%EC%9E%AC%EA%B7%80)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/38 함수형 프로그래밍에서 부작용(side effect)와 참조 투명성은 무엇인가?

## 💡함수형 프로그래밍의 주요 개념

### 1. 부수효과(= 부작용, Side Effect)

> 함수 내부의 코드 실행으로 인해 함수 외부가 영향을 받는 것을 의미한다.
> 
- 참조에 의한 파라미터를 수정하는 경우
- 예외를 던지거나 오류를 발생시켜 실행을 중단하는 경우
- 콘솔 혹은 파일에 읽기/쓰기(I/O)하는 경우
- 서버와 통신하는 경우

### 2. 참조 투명성(****Referential Transparency****)

> 프로그램의 변경 없이도 어떤 표현식을 값으로 대체할 수 있음을 의미한다.
> 
> 
> 함수의 결과는 입력 파라미터에만 의존하고 함수의 외부 상태(입력 콘솔, 파일, 원격 URL, 데이터베이스, 파일 시스템 등)에 의존하지 않아야 한다.
> 
- 동일한 인자에 대해 항상 동일한 결과를 반환해야 한다.
- 함수의 인자값 혹은 함수 외부의 데이터를 변경하지 않아야 한다.
- 예외를 던지지 않아야 한다.

## Reference

- [부수 효과 (Side Effect), 참조 투명성 (Referential Transparency)](https://jinwooe.wordpress.com/2017/12/21/%EB%B6%80%EC%88%98-%ED%9A%A8%EA%B3%BC-side-effect-%EC%B0%B8%EC%A1%B0-%ED%88%AC%EB%AA%85%EC%84%B1-referential-transparency/)
- [1. 함수형 프로그래밍 기초 개념](https://enhancement.tistory.com/2)
- [함수형 프로그래밍에 관해](https://medium.com/humanscape-tech/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EC%97%90-%EA%B4%80%ED%95%B4-7f6172599fc)
