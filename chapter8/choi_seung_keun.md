# Q. Iterator, Entry 특징과 주요 메서드는 어떤 것이 있을까?

## 1. Iterator

### 정의

- 객체 지향 프로그래밍에서 배열과 같은 여러 데이터 집합으로 이루어진 자료구조의 내부 요소를 순회하는 객체
- 자바의 iterator는 컬렉션 프레임워크에서 **저장된 요소를 순회하며 읽어오는 방법을 표준화** 한 것

### 구조
![image](https://github.com/WeeklyStudy/modern-java-in-action/assets/77659341/3da48241-aac1-4149-b3aa-cfb4237ba84c)

- 컬렉션은 데이터를 처리하는 방식을 구조화해놓은 인터페이스로 List, Set, Queue, Deque 등이 상속받고 있음
- 컬렉션 인터페이스를 상속받는 클래스에 대해 Iterator 인터페이스 사용이 가능하다.

### 주요 메서드

```java
public interface Iterator{
	boolean hasNext();
	Object next();
	void remove();
}
```

- boolean hasNext() : 이후에 요소가 더 있는지를 체크하는 메서드, 요소가 있다면 true를 반환
- Object next() : 다음에 있는 요소를 반환
- remove() : next()로 읽어온 요소를 삭제

`메소드의 호출 순서` : **hasNext() -> next() -> remove()**

### 사용 방법

```java
LinkedList<Integer> lnkList = new LinkedList<Integer>();

lnkList.add(4);
lnkList.add(2);
lnkList.add(3);
lnkList.add(1);

// iterator 객체 호출
// iterator는 lnkList의 값(요소)들을 가져옴
Iterator<Integer> iterator = lnkList.iterator();

while (iter.hasNext()) {
	Integer nextNum = iterator.next();
	System.out.println(nextNum);
}
```

### 사용하는 이유

- 컬렉션의 구현 방법을 노출시키지 않으면서도 집합체 내부에 있는 모든 항목에 접근할 수 있는 방법을 제공
    - 컬렉션 객체 내부의 모든 요소에 접근하는 방법을 통일되어 있으면 어떤 종류의 집합체에서도 사용할 수 있는 다형적인 코드를 만들 수 있음 ⇒ 모든 컬렉션은 iterator를 상속
    - 모든 항목에 일일이 접근하는 작업을 컬렉션 객체가 아닌 반복자 객체에서 맡게 되므로, 구현이 간단해질 뿐 아니라, 집합체(컬렉션 객체)에서는 반복 작업이 아닌 객체 컬렉션 관리에 전념할 수 있음

`정리` : **컬렉션 종류와 관계없이 일관성 있게 프로그래밍 할 수 있다는 장점을 가진다.**

## 2. Map.Entry

### 정의

Map 인터페이스에서 저장되는 key-value 쌍을 다루기 위해 내부적으로 정의해놓은 내부 인터페이스

- Map 인터페이스 구현하는 클래스에서는 Map.Entry 인터페이스를 함께 구현해야 함
- Map 저장되는 Entry 조작을 위한 메소드가 정의되어 있음

### 주요 메서드

| boolean equals(Object o) | 동일한 Entyry 인지 비교 |
| --- | --- |
| Object getKey() | Map.Entry 객체의 key 값을 반환 |
| Object getValue() | Map.Entry 객체의 value 값을 반환 |
| int hashCode() | Map.Entry 객체의 해시코드를 반환 |
| Object setValue(Object value) | Map.Entry 객체의 key에 대한 value 값을 수정 |

### 사용 방법

- getKey, getValue 사용
    
    ```java
    HashMap<Integer, String> map = new HashMap<Integer, String>();
    map.put(1, "a");
    map.put(2, "b");
    map.put(3, "c");
    
    Set<Entry<Integer, String>> entrySet = map.entrySet();
    
    for(Entry<Integer, String> entry : entrySet) {
    	System.out.println("key : " + entry.getKey() + " / value:" + entry.getValue());
    }
    ```
    
- iterator 사용
    
    ```java
    Iterator<Entry<Integer, String>> it = map.entrySet().iterator();
    
    while(it.hasNext()) {
    	Entry<Integer, String> entry = it.next();
    
    	System.out.print("key-value : " + entry + " / ");
    	System.out.println("key : "+entry.getKey()+" / value : "+entry.getValue());
    }
    ```
    

### 사용 이유

- entry 객체는 Map의 key-value 쌍을 하나의 객체로 가지고 있어 서로 연관되어 있다는 것을 명시할 수 있고 있다.
- 주요 메서드를 통해 key, value 쉽게 접근 가능하며 value 값을 쉽게 수정 가능하다.
    
    일반적인 Map에서 key에 대한 value 값을 수정할 때, key를 통해 value를 얻어와야 하는 번거로움 존재
    

### 참고 자료

- iterator 관련
    - [이터레이터 패턴 (iterator pattern*](https://jusungpark.tistory.com/25)
    - [Iterator란? Iterator 사용 이유](https://tlatmsrud.tistory.com/61)
    - [Java - Iterator가 존재하는 이유](https://oblivionsb.tistory.com/20)
- entey 관련
    - [Java Map.Entry 사용하기](https://seungjjun.tistory.com/m/270?category=1065281)
    - [[Java/자바] 해시맵, HashMap 사용법과 EntrySet, Iterator](https://lasbe.tistory.com/63)
    - [Map.Entry Java 클래스 사용](https://recordsoflife.tistory.com/314)

</br>

# Q. ArrayList.asList() vs List.of()

### 리스트를 만드는 방법

- 생성자를 통한 인스턴스화
- ArrayList.asList()
- List.of()

```java
public static void main(String[] args) {
    // 생성자 방식
    List<Number> arrayList = new ArrayList<>();
    arrayList.add(1);
    arrayList.add(2);
    arrayList.add(3);

    // Arrays 클래스의 asList 메서드
    List<Number> asList = Arrays.asList(1, 2, 3);

    // List 인터페이스의 of 메서드 (jdk 9)
    List<Number> listOf = List.of(1, 2, 3);
}
```

## ArrayList.asList(), List.of() 차이점

1. 리스트 변경 가능 여부
    
    ArrayList.asList()와 List.of() 만든 List 는 불변 리스트이게 된다.
    
    |  | 원소 추가/삭제 | 변경(set) |
    | --- | --- | --- |
    | new ArrayList<>() | 가능 | 가능 |
    | Arrays.asList() | 불가능 | 가능 |
    | List.of() | 불가능 | 불가능 |
    - asList() : 요소를 변경하는 set()  동작 가능하지만 요소를 추가 삭제하는 동작은 불가능한 불변리스트
        - 내부적으로 set() 구현되어 있지만 add, remove 구현되어 있지 않음
        - 반환 값이 java.util 패키지의 ArrayList 아닌 Arrays 내부의 ArrayList 이다.
    - of() : 원소를 추가/삭제, 변경 동작이 불가능한 불변 리스트
        - UnsupportedOperationException 발생
        - 내부적으로 add, remove, set 구현되어 있지 않음
        - 반환 값이 java.util 패키지의 ArrayList 아닌 ImmutableCollections 내부 클래스인 ListN 객체
    
    `불변 객체의 이점`
    
    ArrayList.asList(), List.of() 불변 객체 구성된 이유는 불변 객체만의 이점을 이용해 다른 컬렉션으로 용이하게 변환하기 위함
    
    - 스레드 안정성 : 불변 객체는 동기화 없이도 여러 스레드에서 안전하게 공유하고 액세스 가능
    - 코드 간소화 : 불변 개체는 동시성을 위해 설계할 필요가 없으므로 코드가 간소화되고 버그 가능성이 낮음
    - 향상된 성능 : 항상 동일한 상태를 유지하므로 캐시 및 재사용 용이

### 1. 리스트 내부 배열 참조 여부
- List.of 는 참조한 원본 배열 값이 바뀌어도 리스트 값은 바뀌지 않음     
⇒ 배열 원소를 순회하여 새로 만든 배열 변수에 대입하기에 불변성을 가짐
        
    - Arrays.asList는 참조한 원본 배열의 값이 바뀌면 리스트의 값도 바뀌고, 반대로 List의 값을 수정해도 원본 배열의 값도 바뀌게 된다. ⇒ 배열 래퍼런스 변수 그대로 참조하고 있음
### 2. null 값 가질 수 있는지 여부
- ArrayList.asList 는 원소에 null 값 가질 수 있음
- List.of 는 원소에 null 값 가질 수 없음
### 3. 메모리 사용량 차이
변경 불가능한 컬렉션 인스턴스는 변경 가능한 인스턴스보다 적은 메모리 사용
    
- 완전 불변인 List.of() 사용이 권장 됨

### 참고자료

- [https://inpa.tistory.com/entry/JAVA-☕-ArraysasList-와-Listof-차이-한방-정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-ArraysasList-%EC%99%80-Listof-%EC%B0%A8%EC%9D%B4-%ED%95%9C%EB%B0%A9-%EC%A0%95%EB%A6%AC)

</br>

# Q.팩토리 메서드란 무엇인가?

### 정의

- **객체의 생성을 담당하는 클래스 메서드**
- 클래스 내에 선언되어있는 메서드를 내부의 `new`를 이용해 객체를 생성해 반환
    
    ⇒ 정적 팩토리 메소드를 통해서 `new`를 간접적으로 사용
    

### 특징

- 이름을 가질 수 있음
- 호출 할 때마다 새로운 객체를 생성할 필요가 없음
- 하위 자료형 객체를 반환할 수 있음
- 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환 가능
- **정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 됨**
