## Q1. **인터페이스 사용 시 주의사항에는 무엇이 있을까?**

### 1. 인터페이스 간의 상속 - extends 키워드

- 인터페이스를 인터페이스로 구현(implement) 할 수 없다. 상속(extends) 받아야 한다.
- 즉, 인터페이스 자체를 **확장**시키고 싶다면 `implements`가 아닌 `extends` 키워드를 통해 인터페이스를 상속해야 한다.

### 2. 다중 상속 - 다이아몬드 문제

- Java8 이전에는 클래스와 달리 인터페이스는 구현부가 없었기 때문에 다중 상속에도 충돌 가능성이 없었다.
- 그러나 디폴트 메서드가 도입된 이후 다이아몬드 문제가 발생할 수 있으니 명시적으로 오버라이딩하여 사용하거나, 원하는 디폴트 메서드를 호출하도록 해야 한다.

### 3. 인터페이스 상수 필드 상속 관계

- 인터페이스의 필드들은 모두 `public static final`이다.
- 클래스의 상속일 경우 상위 클래스 필드 멤버가 하위 클래스 필드 멤버에 덮어 씌워지지만, 인터페이스의 필드는 상수로 취급되기 때문에 구현체에서 값을 변경할 수 없다.
- 따라서 **인터페이스의 필드는 구현체를 따라가지 않는다.**

### 4. ****인터페이스 일부 구현 (추상 클래스)****

- 만일 클래스가 구현하는 인터페이스의 메서드 중 **일부만 구현**한다면  `abstract`를 붙여서 추상 클래스로 선언해야 한다.

### 5. 제어자 생략

- `public static final` 과 `public abstract` 제어자는 생략이 가능하다.
    
    ```java
    // 생략 전
    interface MyInterface {
        public static final 타입 상수명 = 값;
        public abstract 타입 메서드명(매개변수목록);
    }
    
    // 생략 후
    interface MyInterface {
        타입 상수명 = 값;
        타입 메서드명(매개변수목록);
    }
    ```
    

### 6. 디폴트 메서드로 제공 불가능한 메서드

- 인터페이스는 **Object 클래스를 상속받지 않는다.**
- 따라서, Object 클래스가 제공하는 메서드 `toString()`**,** `equals()`, `hashcode()` 와 같은 메서드들은 **디폴트 메소드**로 제공할 수 없다.
    
    ```java
    public interface Foo  {
    
        void printValue();
    		
        // 컴파일 오류
        default String toString() {
    
        };
    
    }
    ```
    
- **Object 클래스가 제공하는 메서드를 재정의해야 하는 경우, 구현 클래스에서 재정의해야 한다.**

### 7. **인터페이스의 인스턴스화 불가능**

- 인터페이스는 추상적인 개념을 나타내는 것이기 때문에 `new MyInterface()`와 같은 방식으로 직접적으로 인스턴스화할 수 없다.
- 인터페이스를 구현하는 클래스를 통해 해당 인터페이스의 객체를 생성해야 한다.

### 8. 인터페이스 형태의 배열

- 인터페이스는 스스로 인스턴스화는 불가능하지만, 인터페이스의 다형성(구현 클래스들의 객체)를 통해 인터페이스 형태의 배열을 선언하고 관리할 수 있다.
- 단, 들어가는 객체는 실제 인스턴스여야 하므로, 해당 인터페이스를 구현한 클래스만 들어갈 수 있다.
- 세간에는 인터페이스를 자료형으로 쓰는 습관을 들이면 훨씬 유용하다고 한다.
- [예제 코드](https://nozeroslope.tistory.com/221)

### 9. [권장 사항] 공개된 요소의 API에는 항상 문서화 주석 작성하기 - `@implSpec`

- `@implSpec` 주석은 해당 메서드와 하위 클래스 사이의 계약을 설명하여, 하위 클래스들이 그 메서드를 상속하거나 `super` 키워드를 이용해 호출할 때 그 메서드가 어떻게 동작 하는지를 명확히 인지하고 사용하도록 해줘야 한다.
- 따라서, 오픈 API로 인터페이스를 제공할 때, 인터페이스의 디폴트 메서드에 `@implSpec` 주석을 통해 문서화해줘야 한다.
    
    ```java
    /**
     * Returns a sequential {@code Stream} with this collection as its source.
     *
     * <p>This method should be overridden when the {@link #spliterator()}
     * method cannot return a spliterator that is {@code IMMUTABLE},
     * {@code CONCURRENT}, or <em>late-binding</em>. (See {@link #spliterator()}
     * for details.)
     *
     * @implSpec
     * The default implementation creates a sequential {@code Stream} from the
     * collection's {@code Spliterator}.
     *
     * @return a sequential {@code Stream} over the elements in this collection
     * @since 1.8
     */
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }
    ```
    

### 10. **객체를 클래스가 아닌 인터페이스로 참조할 때의 주의점**

- 책 Effective Java에서는 ‘***객체는 클래스가 아닌 인터페이스로 참조하라***’라고 한다.
- 객체를 클래스가 아닌 인터페이스로 참조하면, 구현 타입이 변경되도 유연하게 대처할 수 있기 때문이다.
- 즉, 인터페이스를 참조하여 만든 객체는 구현타입이 변하더라도 인터페이스에 있는 메서드를 그대로 사용할 수 있다.
- 만일 적한한 인터페이스가 없다면 클래스의 계층구조 중 필요한 기능을 만족하는 가장 덜 구체적인(상위의) 클래스를 타입으로 사용하는 것이 권장된다.
<img src="https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/b950513b-f426-4f76-96c0-fcb058aee7fe" width="650">

```java
public class LinkedHashSet<E>
    extends HashSet<E>
    implements Set<E>, Cloneable, java.io.Serializable {
```
```java
//좋은 예 
Set<String> set = new LinkedHashSet<>();

//나쁜 예
LinkedHashSet<String> set = new LinkedHashSet<>();
```

- 하지만 주의점이 있다. 업캐스팅을 사용하게되면 상위 인터페이스의 구현체(HashSet, LinkedHashSet)를 사용할 수 있지만 LinkedHashSet이 가지고 있는 추가적인 메서드나 기능은 Set 인터페이스의 범위를 벗어나므로, **Set으로 업캐스팅한 경우에는 이러한 추가적인 기능에 접근할 수 없다.**


### References

- [[Java] - 인터페이스 Default Methods, Static Methods](https://velog.io/@roro/Java-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-Default-Methods-Static-Methods)
- [인터페이스(Interface) 문법 & 활용 - 완벽 가이드](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4Interface%EC%9D%98-%EC%A0%95%EC%84%9D-%ED%83%84%ED%83%84%ED%95%98%EA%B2%8C-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC)
- [자바 스태틱(클래스변수(메소드))와 상속 클래스의 주의점 - 가자미](https://nongue.tistory.com/23)
- [인터페이스의 타입 변환과 다형성](https://nozeroslope.tistory.com/221)
- [아이템56 공개된 API 요소에는 항상 문서화 주석을 작성하라](https://incheol-jung.gitbook.io/docs/study/effective-java/undefined-6/56-api)
- [[자바]인터페이스 주의사항](http://m.blog.naver.com/skykingkjs/150144018727)
- [[이펙티브 자바] 객체는 인터페이스를 사용해 참조하라](https://jgrammer.tistory.com/entry/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EA%B0%9D%EC%B2%B4%EB%8A%94-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%B4-%EC%B0%B8%EC%A1%B0%ED%95%98%EB%9D%BC)
- [How to Learn Java Collections – A Complete Guide](https://www.geeksforgeeks.org/how-to-learn-java-collections-a-complete-guide/)

## Q2. **자바8에 도입된 인터페이스의 default 메서드와 static 메서드**

### 1. **default 메서드**

- 개념 및 도입 배경
    - **인터페이스**에 **새로운 메서드**가 **추가**되어도, 구현 클래스를 수정하지 않고 깔끔하게 **하위 호환**하기 위해 **인터페이스 내부에서** **기본 구현**을 제공하는 디폴트 메서드가 도입되었다.
- 특징
    - **public** 메서드
        - 기본 메서드를 포함하여 인터페이스의 모든 메서드 선언은 암시적으로 public이다.
    - **구현 필수**
        - 디폴트 메서드는 추상 메서드(구현 없이 선언된 메서드)가 아니기 때문에 **바디가 반드시 구현**되어야 한다.
        - 이로 인해 디폴트 메서드는 기존의 추상 메소드에 기능을 덧붙이는 목적으로 사용할 수 있다.
    - **강제 상속**
        - 인터페이스를 구현하는 클래스에서 **구현하지 않은 메서드**는 **인터페이스 자체에서 기본으로 제공한다.**
    - **오버라이딩 가능**
        - 상위 인터페이스에서 구현된 디폴트 메서드는 서브 인터페이스나 서브 클래스에서 재정의(overriding)될 수 있다.
    - **super** 연산자
        - super 연산자를 사용하여 상위 인터페이스의 디폴트 메서드를 호출할 수 있다. ([코드 예제](https://devfunny.tistory.com/413))
- 활용
    - **선택형 메서드(optional method)**
        - 사용하지 않는 메서드를 디폴트 메서드로 구현해 사용하지 않는 클래스에서는 구현을 할 필요가 없어져 불필요한 코드를 줄일수 있다.
    - **동작 다중 상속**
        - 클래스는 여러 디폴트 메서드를 상속 받을 수 있으므로, 다중 상속이 불가능했던 이전 Java와 달리 Java8 이후에는 동작 다중 상속이 가능해진다.
        - 따라서, 인터페이스를 조합해서 다양한 클래스를 구현할 때에는 **다이아몬드 문제**를 조심해야 한다.
        - 같은 오버라이드 조건(메서드명, 매개변수)이 동일한 메서드가 있다면 super을 통해 명시적으로 위임해줘야 한다.

### 2. static 메서드

- 도입 배경 및 개념
    - 디폴트 메서드와 마찬가지로 인터페이스 구현 클래스에 영향을 주지 않으면서도 인터페이스에 새로운 기능을 제공하기 위해 **인터페이스 내부에서 인스턴스 생성 없이도 호출 가능한** static 메서드를 정의할 수 있게 되었다.
- 특징
    - **public** 메서드
        - static 메서드를 포함하여 인터페이스의 모든 메서드 선언은 암시적으로 public이다.
    - **static**
        - 클래스의 모든 인스턴스는 static 메서드를 공유한다. static 메서드는 object가 아니라 정의된 클래스와 연결된 메서드이기 때문이다.
    - **오버라이딩 불가**
        - 클래스 간의 상속에서도 하위 클래스의 메서드 overriding은 인스턴스 메서드에 한해서만 가능하다.
            - 하위 클래스에서 상위 클래스의 static 메서드 재정의가 가능하긴 하지만, 이는 오버라이딩에 의한 재정의가 아닌 hiding(부모 클래스의 메서드가 가려진 상태)에 의한 재정의이다.
                - overriding인지 hiding인지 판별할 수 있는 기준은 `@Override` 어노테이션 사용 가능 여부다.
                - 클래스의 static 메서드 재정의 시, `@Override` 어노테이션을 사용하면 컴파일 에러가 난다.
            - 마찬가지로, 인터페이스의 static 메서드도 상속되지 않는다.
            - 따라서, 메서드 호출 시 `InterfaceName.staticMethod()` 로 호출가능하다.
- 활용
    - **helper 메서드**
        - 라이브러리에서 helper 메서드를 더 쉽고 결합도 높게 구성할 수 있다.

### 3. 결론

- 기존의 Java interface에서는 abstract 메소드만을 가질 수 있었던 반면, Java 8 이후부터는 default, static 메소드도 추가로 정의할 수 있게 되었다.
- 인터페이스의 디폴트 메서드와 정적 메서드는 자바 API의 호환성을 유지하면서 라이브러리를 바꿀 수 있기 때문에 주로 라이브러리 설계자들에 의해 많이 사용된다.
- 일반 개발자들은 인터페이스의 디폴트 메서드와 정적 메서드 덕분에 라이브러리를 더욱 편리하게 사용할 수 있게 되었다.
    - 예시로, List 인터페이스의 디폴트 메서드인 sort 메서드는 Collections.sort 메서드를 호출하는데, 이 덕에 Collections가 아닌 List에서 직접 sort 메서드를 호출할 수 있게 되었다.

### References

- [The Java™ Tutorials - Interface default method, static method](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)
- [인터페이스의 default, static 메서드](https://yeon-kr.tistory.com/187)
- [인터페이스에서의 static 메소드와 default 메소드(Static and Default Methods in Interfaces in Java)](https://soft-dino.tistory.com/25)
- [[JAVA] interface와 abstract 차이점과 java8의 interface, 다중상속](https://kangyb.tistory.com/14)
- [static은 상속이 될까? *](https://jinyoungchoi95.tistory.com/16)

## Q3. **자바에서 다중 상속을 허용하지 않는 이유는 무엇일까?**

### 1. 다이아몬드 문제

- 다중 상속의 가장 대표적인 문제로 꼽히는 문제는 다이아몬드 문제(Diamond Problem)**다.**
- **부모 요소 중** **같은** **Method Signature(메서드 이름 + 파라미터 리스트)를 가지는 메서드가 있을 때, 어떤 부모의 메서드를 호출해야할지 모호해지는 문제가 발생한다.**

### 2. 인터페이스의 다중 상속
- Java8 이전에도 인터페이스는 다중 상속이 가능했다.
- 메서드의 기능에 대해 선언만 해두기 때문에(추상 메서드) 충돌할 여지가 없었기 때문이다.
  ```java
    interface Movable {
  	/** 지정된 위치(x, y)로 이동하는 기능의 메서드*/
  	void move(int x, int y); // public abstract 생략
  }

  interface Attackable {
	  /** 지정된 대상(u)을 공격하는 기능의 메서드 */
	  void attack(Unit u);
  }

  interface Fightable extends Movable, Attackable {} // 멤버 2개 
  ```

### 3. 동작 다중상속 - 디폴트 메서드

- 현재 Java에서 클래스 간의 다중 상속을 시도하면 컴파일 타임에 오류가 발생한다.
- 하지만, Java8 이후 인터페이스 내부에 디폴트 메서드가 선언가능해지면서 동작 다중 상속(구현체의 다중 상속)도 가능해졌다.
- 만약 기능이 중복될 경우, 클래스의 디폴트 메서드가 우선권을 갖고, 그 다음에는 서브 인터페이스의 디폴트 메서드가 우선권을 갖는다.
- 디폴트 메서드를 구현한 인터페이스 A를 extends한 서브 인터페이스 B, C를 클래스 D가 상속받는다면,  B나 C가 그대로 상속받은 디폴트 메서드는 클래스 D에서도 호출가능하고, 충돌이 일어나지 않는다.
   - 인터페이스 A로부터 서브 인터페이스 B,C가 그대로 상속받은 디폴트 메서드는 구현부가 같기 때문에 문제 여지가 없다.
- 하지만, 위 상황에서 B나 C 인터페이스에서 디폴트 메서드를 오버라이드한 인터페이스가 있다면, 결국 (서브 인터페이스에서 상속한) 디폴트 메서드 vs (서브인터페이스에서 구현한)가 충돌하는 구조가 되어버린다.
- 따라서, 위와 같이 동등한 우선권을 지닌 디폴트 메서드가 충돌할 경우에는 클래스 D가 어떤 메서드를 사용할지 **명시적으로 오버라이드**하여 문제를 해결해야 한다.

### References

- [자바와 다중상속 문제 - 피자먹는 개발자](https://castlejune.tistory.com/30)
- [자바에서 클래스 다중상속을 막은 이유](https://gyubgyub.tistory.com/102)
- [[Java] 자바8에 도입된 인터페이스의 디폴트 메서드 (Default Method)](https://loosie.tistory.com/712)
- [Java] 자바의 다중상속 (feat. 인터페이스와 추상클래스)
- [자바의 정석 ch7.7 인터페이스(interface)](https://velog.io/@balparang/%EC%A0%95%EC%84%9D-ch7.7-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4interface)
