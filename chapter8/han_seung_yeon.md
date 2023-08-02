# https://github.com/WeeklyStudy/modern-java-in-action/issues/20 Arrays.asList() vs List.of()
    
## 💡Arrays.asList()란?

> 고정된 크기의 리스트를 생성한다.
> 

### 0. Arrays.asList()의 반환 타입: ArrayList

- `Arrays.asList()` 의 반환 타입은 `java.util.ArrayList` 가 아닌 `Arrays 클래스 내부클래스의 ArrayList` 다.
- `ArrayList`는 `배열` 구조로 이루어졌다.

  <img width="570" alt="스크린샷 2023-07-31 오후 3 33 14" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/c4d8b7e6-271b-4244-8524-9f3dca7878a6">

### 1. set은 가능하지만 add/remove 시 **UnsupportedOperationException이 발생한다.**

```java
@Test
@DisplayName("[Arrays.asList] 요소 변경은 가능하지만 추가/삭제는 불가능하다.")
void t1() {
    // given
    Integer[] array = new Integer[]{1, 2, 3, 4};
    List<Integer> list = Arrays.asList(array);
    // 추가X
    Assertions.assertThrows(UnsupportedOperationException.class, () -> {
        list.add(5);
    });
    // 삭제X
    Assertions.assertThrows(UnsupportedOperationException.class, () -> {
        list.remove(1);
    });
    // 변경O
    list.set(3, 5);
    assertThat(list).containsExactly(1,2,3,5);
}
```

- `AbstractList` 클래스를 상속받은 `ArrayList` 는 `set()` 을 오버라이드하여 변경이 가능하고 `add()` , `remove()` 는 오버라이드하지 않아 `UnsupportedOperationException` 이 발생한다.

  <img width="368" alt="스크린샷 2023-07-31 오후 2 29 35" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/d7c830fb-8cac-4a96-ae83-85fef200c07e">
  <img width="365" alt="스크린샷 2023-07-31 오후 2 29 52" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/1a804162-813e-4687-94b5-058d423898a8">
  <img width="294" alt="스크린샷 2023-07-31 오후 2 30 26" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/4b9f91b8-3529-4962-a60d-b614e03481ef"> 

### 2. array가 null이면 NullPointerException이 발생하고 각 원소가 null인 것은 가능하다.

```java
@Test
@DisplayName("[Arrays.asList] array의 null을 허용하지 않고 원소의 null은 허용한다.")
void t2() {
    // given
    Integer[] array1 = null;
    Integer[] array2 = new Integer[]{1, null, 3, null};
    // array의 null 허용X
    Assertions.assertThrows(NullPointerException.class, () -> {
        Arrays.asList(array1);
    });
    // array 원소의 null 허용
    List<Integer> list = Arrays.asList(array2);
    assertThat(list).containsExactly(1, null, 3, null);
}
```

- `Arrays.asList()` 의 파라미터로 들어온 배열이 null인지는 검사하지만 각 원소가 null인지는 검사하지 않는다.
  <img width="302" alt="스크린샷 2023-07-31 오후 2 44 09" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/3120a6c8-f871-48d8-afa8-00c7bb164c50">
  <img width="343" alt="스크린샷 2023-07-31 오후 2 45 13" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/d12a628e-ffae-4bbd-ad24-6652250f5fb8">

### 3. 배열 혹은 리스트 원소값이 변경되면 다른 한 쪽에도 영향을 준다.

```java
@Test
@DisplayName("[Arrays.asList] array와 list는 서로 영향을 주고 받는다.")
void t3() {
    // given
    Integer[] array = new Integer[]{1, 2, 3, 4};
    List<Integer> list = Arrays.asList(array);
    // array 변경
    array[3] = 5;
    assertThat(list).containsExactly(1,2,3,5);
    // list 변경
    list.set(0, 0);
    assertThat(array).containsExactly(0,2,3,5);
}
```

- `ArrayList` 객체는 인자로 받은 배열의 값을 복사하는게 아니라 `참조값`을 복사한다.(얕은복사)
- 원본 배열 값이 바뀌면 리스트 값도 바뀌고 리스트 값을 바꾸면 원본 배열 값도 변경된다.

## 💡List.of()란?

> 자바9부터 지원하는 메서드로 불변 리스트를 생성한다.
> 

### 0. List.of()의 반환 타입: List12, ListN

- 인자값이 1, 2개일 때는 `ImmutableCollections.List12` 객체를 반환한다. `변수` 2개로 관리한다.
    <img width="482" alt="스크린샷 2023-07-31 오후 3 42 58" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/1e2d2811-c201-46f6-9c06-0cf3c6736a1b">
    
- 인자값이 3개 이상일 때는 `ImmutableCollections.ListN` 객체를 반환한다. 내부는 `배열`로 관리한다.
    <img width="480" alt="스크린샷 2023-07-31 오후 3 42 32" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/9812dccf-6dc0-4ea9-b2af-fe01c0f0b138">

### 1. add/remove/set 시 **UnsupportedOperationException이 발생한다.**

```java
@Test
@DisplayName("[List.of] 추가/삭제/변경이 불가능하다.")
void t4() {
    // given
    Integer[] array = new Integer[]{1, 2, 3, 4};
    List<Integer> list = List.of(array);
    // 추가X
    Assertions.assertThrows(UnsupportedOperationException.class, () -> {
        list.add(5);
    });
    // 삭제X
    Assertions.assertThrows(UnsupportedOperationException.class, () -> {
        list.remove(1);
    });
    // 변경X
    Assertions.assertThrows(UnsupportedOperationException.class, () -> {
        list.set(3, 5);
    });
}
```

- `AbstractImmutableList` 를 상속받은 `List12`, `ListN` 은 `add()`, `remove()`, `set()` 을 오버라이드하지 않아 모두 `UnsupportedOperationException` 이 발생한다.
    <img width="712" alt="스크린샷 2023-07-31 오후 3 48 51" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/3a7b3d4d-7ca6-4e6d-baad-5342710c49db">
    

### 2. 원소값이 null이면 NullPointerException이 발생한다.

```java
@Test
@DisplayName("[List.of] 각 원소의 null을 허용하지 않는다.")
void t5() {
    // 파라미터 null 허용X
    Assertions.assertThrows(NullPointerException.class, () -> {
        List.of(null);
    });
    //
    Integer[] array = new Integer[]{1, 2, null, 4};
    // 원소 null 허용X
    Assertions.assertThrows(NullPointerException.class, () -> {
        List.of(array);
    });
}
```

- `List.of()` 의 파라미터로 들어온 값들이 null인지 모두 검사한다.

  <img width="334" alt="스크린샷 2023-07-31 오후 4 10 37" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/925b1f30-6f0f-44be-a133-0a2dc05e62d3">
  <img width="605" alt="스크린샷 2023-07-31 오후 4 01 52" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/24ad29fb-f76a-4d21-8626-abf72ccdc4d6">


### 3. 원본 값을 변경해도 리스트는 영향을 받지 않는다.

```java
@Test
@DisplayName("[List.of] 원본 값을 변경해도 리스트는 영향을 받지 않는다.")
void t6() {
    // given
    Integer[] array = new Integer[]{1, 2, 3, 4};
    List<Integer> list = List.of(array);
    // 원본 배열 변경
    array[3] = 5;
    assertThat(list).containsExactly(1,2,3,4);
}
```

- `List12` , `ListN` 객체는 인자로 받은 `값`을 복사한다.(깊은복사)
- 원본 값이 바뀌어도 리스트 값은 변하지 않는다.

## 💡결론

|  | Arrays.asList() | List.of() |
| --- | --- | --- |
| 삽입(add) | X | X |
| 삭제(remove) | X | X |
| 변경(set, replace) | O | X |
| 원소 null 허용 여부 | O | X |
| 참조 여부 | O(참조값 복사) | X(값에 의한 복사) |
| 불변 객체 여부 | X | O |
| thread-saftety 여부 | O | X |
- `Arrays.asList` 는 크고 동적인 데이터에 사용하고 `List.of` 는 작고 변경되지 않는 데이터에 사용한다.
- 일반적으로 `Arrays.asList` 보다 `List.of` 의 메모리 사용량이 더 적기 때문에 `List.of` 를 사용하는 것을 권장한다.
- `Arrays.asList` 는 값 변경시 원본 배열에 영향을 줄 수 있기 때문에 찾기 어려운 버그를 발생시킬 수 있으므로 사용에 주의하자.

## Reference

- [[JAVA] - Arrays.asList()와 List.of()의 차이*](https://kim-jong-hyun.tistory.com/31)
- [Java - Arrays.asList vs List.of 차이 (완벽 정리)!*](https://jaehoney.tistory.com/144)
- [Arrays.asList() 와 List.of() 차이 한방 정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-ArraysasList-%EC%99%80-Listof-%EC%B0%A8%EC%9D%B4-%ED%95%9C%EB%B0%A9-%EC%A0%95%EB%A6%AC)
- [[JAVA] Arrays.asList()와 List.of()의 차이](https://atoz-develop.tistory.com/entry/JAVA-ArraysasList%EC%99%80-Listof%EC%9D%98-%EC%B0%A8%EC%9D%B4)
- [Difference Between Arrays.asList() and List.of()](https://www.baeldung.com/java-arrays-aslist-vs-list-of)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/21 Iterator, Entry 의 특징과 주요 메서드는 어떤것이 있을까?
    
## 💡Iterator(반복자)란?

> 컬렉션의 내부 요소를 순회하는 객체다.
> 

## 💡for-each 와 비교한 iterator 특징

### 1. 탐색 방향

- `for-each` 문은 처음부터 끝까지 `순방향`으로 탐색한다.
    
    ```java
    @Test
    @DisplayName("[for-each] 순방향으로만 탐색 가능하다.")
    void t9() {
        // given
        ArrayList<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
    
        // 순방향 순회
        for(int n : list) {
            System.out.println(n);
        }
    }
    ```
    
- `ListIterator` 는 `next()` 로 `순방향` 요소에, `previous()` 로 `역방향` 요소에 접근할 수 있다.(양방향)
    
    ```java
    @Test
    @DisplayName("[iterator] 양방향으로 탐색 가능하다.")
    void t10() {
        // given
        ArrayList<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
    
        ListIterator<Integer> listIterator = list.listIterator();
        // 순방향 순회
        while(listIterator.hasNext()) {
            System.out.print(listIterator.next());
        }
        System.out.println();
        // 역방향 순회
        while(listIterator.hasPrevious()) {
            System.out.print(listIterator.previous());
        }
    }
    ```
    

### 2. 요소 삭제

- `for-each` 문에서 요소를 삭제하면 `ConcurrentModificationException` 예외가 발생한다.
    
    ```java
    @Test
    @DisplayName("[for-each] 요소 삭제할 수 없다.")
    void t7() {
        // given
        ArrayList<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
    
        // for-each
        Assertions.assertThrows(ConcurrentModificationException.class, () -> {
            for (int n : list) {
                list.remove(n);
            }
        });
    }
    ```
    
- `iterator`는 `remove()` 로 요소를 삭제할 수 있다.
    
    ```java
    @Test
    @DisplayName("[iterator] 요소 삭제할 수 있다.")
    void t8() {
        // given
        ArrayList<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
    
        // iterator
        Iterator<Integer> iterator = list.iterator();
        while(iterator.hasNext()) {
            int next = iterator.next();
            if(next == 2) iterator.remove();
        }
        assertThat(list).containsExactly(1, 3);
    }
    ```
    

## 💡Iterator 주요 메서드

| 인스턴스 메소드 | 반환 타입 | 기능 |
| --- | --- | --- |
| hasNext() | boolean | 다음 요소가 있으면 true, 없으면 false를 반환한다. |
| next() | E | 다음에 있는 요소를 반환한다. |
| remove() | void | next()로 읽어온 요소를 삭제한다. |

## 💡Map.Entry

> Map의 key-value 쌍을 다루기 위해 정의된 Map 인터페이스의 내부 인터페이스다.
> 

## 💡****Map.Entry 인터페이스 주요 메서드****

| 인스턴스 메소드 | 반환 타입 | 기능 |
| --- | --- | --- |
| equals(Object o) | boolean | 동일한 Entry 인지 비교한다. |
| getKey() | K | Entry의 key 객체를 반환한다. |
| getValue() | V | Entry의 value 객체를 반환한다. |
| hashCode() | int | Entry의 해시코드를 반환한다. |
| setValue(V value) | V | Entry의 value 객체를 지정된 객체로 변경한다. |

## Reference

- [[JAVA] Iterator란? Iterator를 사용하는 이유와 사용법*](https://onlyfor-me-blog.tistory.com/319)
- [[자바, Java] Iterator 반복자](https://computer-science-student.tistory.com/343)
- [Iterator 와 For-each 비교](https://erinyees.tistory.com/21)
- [[JAVA]Map.Entry 인터페이스](https://junecode.tistory.com/113)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/22 팩토리 메서드란 무엇인가?
    
## 💡정적 팩토리 메서드(Static Factory Method)란?

> 객체 생성의 역할을 하는 클래스 메서드다. 즉, 클래스에 정적 메서드를 정의하여 생성자 대신 객체를 생성할 수 있게 하는 기법이다.
> 

## 💡정적 팩토리 메서드 장점

### 1. 이름을 가질 수 있다.

> 생성자를 사용한 객체 생성
> 
- `new` 키워드로 객체를 생성하는 생성자는 인자 순서와 내부 구조를 알아야 목적에 맞게 객체를 생성할 수 있다.

```java
class Car {
    private String brand;
    private String color;

    // public 생성자
    public Car(String brand, String color) {
        this.brand = brand;
        this.color = color;
    }
}

public static void main(String[] args) {
    Car bmwRedCar = new Car("BMW", "Red");
}
```

> 정적 팩토리 메서드를 사용한 객체 생성
> 
- 정적 팩토리 메서드는 객체의 생성 목적에 맞게 메서드 네이밍을 하여 직관적이고 이해하기 쉽다.

```java
class Car {
    private String brand;
    private String color;

    private Car(String brand, String color) {
        this.brand = brand;
        this.color = color;
    }

    // 정적 팩토리 메서드
    public static Car brandColorOf(String brand, String color) {
        return new Car(brand, color);
    }
}

public static void main(String[] args) {
    Car bmwRedCar = Car.brandColorOf("BMW", "Red");
}
```

### 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

- 매번 객체를 생성하지 않고 이미 생성된 객체를 반환하여 불필요한 객체 생성을 방지하고 메모리를 절약할 수 있다.
- 생성자를 `private` 으로 설정하여 정적 팩토리 메서드로만 객체 생성을 할 수 있도록 제한할 수 있다.
- `인스턴스 통제 클래스` : 인스턴스의 생성에 관여하여 생성되는 인스턴스의 수를 통제할 수 있는 클래스

```java
class Singleton {
    private static Singleton instance;

    private Singleton() {}

    // 정적 팩토리 메서드
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

public static void main(String[] args) {
    Singleton s1 = Singleton.getInstance();
    Singleton s2 = Singleton.getInstance();
    Singleton s3 = Singleton.getInstance();

    System.out.println(s1);
    System.out.println(s2);
    System.out.println(s3);

    System.out.println(s1 == s2);
    System.out.println(s1 == s3);
}
```

### 3. **하위 타입 객체를 반환할 수 있다.**

- 반환할 객체의 클래스를 자유롭게 선택할 수 있는 유연성이 생긴다.

```java
interface SmarPhone {
    public static SmarPhone getSamsungPhone() {
        return new Galaxy();
    }

    public static SmarPhone getApplePhone() {
        return new IPhone();
    }

    public static SmarPhone getChinesePhone() {
        return new Huawei();
    }
}
```

### 4. 인자에 따라 **다른 클래스의 객체를 반환할 수 있다.**

- 매개변수에 따라 다른 클래스의 인스턴스를 생성해서 반환할 수 있다.(3번 응용 개념)

```java
interface SmarPhone {
    public static SmarPhone getPhone(int price) {
        if(price > 100000) {
            return new IPhone();
        }

        if(price > 50000) {
            return new Galaxy();
        }

        return new Huawei();
    }
}
```

### 5. **정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.**

- 내부 구현을 드러내지 않고 캡슐화 및 정보 은닉을 할 수 있다.
- 반환 타입을 인터페이스로 두면 사용자는 구현체를 몰라도 쉽게 사용할 수 있다.(의존성이 줄어든다.)

## 💡정적 팩토리 메서드 단점

### 1. private 생성자일 경우 상속이 불가하다.

- 상속을 하려면 `public` 혹은 `protected` 생성자가 필요하다.
- 정적 팩토리 메서드를 제공하는 클래스는 생성자의 접근 제어자가 `private` 이기 때문에 하위 클래스를 생성할 수 없다.

### 2. 정적 팩토리 메서드는 프로그래머가 찾기 어렵다.

- 생성자는 API Docs 상단에 있기 때문에 찾기 쉽지만 정적 팩토리 메서드는 다른 메서드와 구분이 없기 때문에 개발자가 직접 찾아야한다.

## 💡정적 팩토리 메서드 네이밍

- `from` : 하나의 매개 변수를 받아서 객체를 생성
- `of` : 여러개의 매개 변수를 받아서 객체를 생성
- `valueOf` : `from`과 `of`의 더 자세한 버전
- `getInstance` | `instance` : 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음
- `newInstance` | `create` : 항상 새로운 인스턴스를 생성
- `get[OrderType]` : 다른 타입의 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음
- `new[OrderType]` : 항상 다른 타입의 새로운 인스턴스를 생성

## Reference

- [정적 팩토리 메서드(Static Factory Method)는 왜 사용할까?*](https://tecoble.techcourse.co.kr/post/2020-05-26-static-factory-method/)
- [정적 팩토리 메서드 패턴 (Static Factory Method)](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%83%9D%EC%84%B1%EC%9E%90-%EB%8C%80%EC%8B%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EC%9E%90)
- [생성자 대신 정적 팩터리 메서드를 고려하라*](https://hudi.blog/effective-java-static-factory-method/)
- [ITEM 1: Static Factory Method(정적 메소드)](https://dahye-jeong.gitbook.io/java/java/effective_java/2021-01-12-static-factory-methods#static-factory-method)
