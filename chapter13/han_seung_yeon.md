# https://github.com/WeeklyStudy/modern-java-in-action/issues/33 자바8에 도입된 인터페이스의 default 메서드와 static 메서드

## 💡자바8에서 인터페이스의 변화

자바8 이전에는 `추상 메서드` 선언 및 `상수` 정의만 가능했다.

하지만 자바8 부터 인터페이스에서 `default 메서드` 와 `static 메서드` 를 이용해 구현부가 있는 메서드를 정의할 수 있게 되었다.

## 💡default 메서드

> default 키워드가 붙은 인터페이스의 구현 메서드다.
> 

### 1. 특징

- 메서드 앞에 `default` 키워드를 붙이고 메서드 구현부 `{…}` 가 있어야 한다.
- 접근 제어자가 `public` 으로 생략 가능하다.
- 인터페이스에서 상속받은 `default 메서드`를 그대로 사용하거나 구현체에서 `재정의`하여 사용할 수 있다.
- 구현체가 모르는 추가 기능을 만드는 것이므로 잘못 사용할 경우 런타임 에러가 발생할 수 있다.
- `@implSpec` javadoc 태그를 통해 `default 메서드` 가 어떤 역할을 하고 어떻게 사용해야 하는지 문서화해주는 것이 좋다.

### 2. 활용

1. 코드 호환성을 유지하면서 새로운 기능을 추가할 수 있다. → `개방-폐쇄 원칙(OCP)` 준수
    - 이전: 인터페이스에 `추상 메서드`를 추가하면 모든 구현체에서 구현을 해야한다.
    - 이후: 인터페이스의 `default 메서드` 로 기본 구현을 제공하면 구현체는 코드 수정을 할 필요가 없다.
2. 불필요한 구현부를 제거할 수 있다.
    - 이전: 인터페이스의 `추상 메서드`는 반드시 구현해야하기 때문에 구현체에서 해당 메서드를 사용하지 않는 경우 빈 메서드로 구현해야 한다.
    - 이후: 인터페이스의 `default 메서드` 를 빈 구현부로 정의하고 해당 메서드가 필요한 구현체에서 재정의하여 사용한다.
3. 동작 다중 상속이 가능하다.
    - 이전: 인터페이스 다중 구현은 가능하지만 추상 메서드만 가질 수 있기 때문에 기능을 상속받지는 못한다. 클래스 다중 상속은 불가하기 때문에 한 클래스로부터 기능을 상속받는다.
    - 이후: 인터페이스는 다중 구현이 가능하고 인터페이스가 구현부(기능)을 포함할 수 있으므로 여러 인터페이스에서 기능(구현 코드)를 상속받을 수 있다. → 다중 상속으로 인한 default method 간 충돌 문제 발생 가능

## 💡static 메서드

> 객체 생성없이 호출할 수 있는 메서드다.
> 

### 1. 특징

- `인터페이스명.메서드명()` 형태로 호출한다.
- 접근 제어자가 `public` 으로 생략 가능하다.
- 구현체에서 `재정의`할 수 없다.

### 2. 활용

1. `유틸리티 메서드`를 제공하기 위해 사용한다.
    - 이전: 인터페이스 내부에 `static 메서드`를 구현할 수 없었기 때문에 `유틸리티 클래스`를 만들어 사용했다.
    - 이후: 인터페이스 내부에 `static 메서드` 로 `유틸리티 메서드`를 구현한다.

## Reference

- [자바8이후 인터페이스의 변경점 2가지와 변경한 이유(default method, static method)](https://jeong-pro.tistory.com/209)
- [인터페이스(Interface) 문법 & 활용 - 완벽 가이드](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4Interface%EC%9D%98-%EC%A0%95%EC%84%9D-%ED%83%84%ED%83%84%ED%95%98%EA%B2%8C-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC)
- [Java 8의 주요 변경 사항과 실무 적용 포인트](https://bbubbush.tistory.com/23)
- [[Java] 자바 8 인터페이스의 Default 메소드와 Static 메소드](https://veneas.tistory.com/entry/Java-%EC%9E%90%EB%B0%94-8-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EC%9D%98-Default-%EB%A9%94%EC%86%8C%EB%93%9C%EC%99%80-Static-%EB%A9%94%EC%86%8C%EB%93%9C)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/34 자바에서 다중 상속을 허용하지 않는 이유는 무엇일까?

## 💡자바의 클래스 다중 상속

자바에서는 클래스의 다중 상속을 지원하지 않는다.

클래스는 메서드 구현부를 포함하고 있기때문에 다중 상속을 지원하면 다이아몬드 문제가 발생할 수 있기 때문이다.

### 1. 다이아몬드 문제
<img width="300" src="https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/cca55d9f-f0eb-4a83-8263-af56fec584e8">

다이아몬드 문제는 다중 상속을 지원하는 언어에서 발생하는 하는 문제다.

B와 C 클래스가 A 클래스를 상속하고, D 클래스가 B와 C 클래스를 다중 상속하는 경우 D 클래스는 B와 C중 어떤 클래스의 메서드를 호출해야하는지 모호함이 생긴다.(메서드 간 충돌이 발생한다)



```java
public class Person {
    void speak() {
        System.out.println("Person");
    }
}

public class Father extends Person {
    @Override
    void speak() {
        System.out.println("Father");
    }
}

public class Mother extends Person {
    @Override
    void speak() {
        System.out.println("Mother");
    }
}

// 컴파일 에러 발생
public class Child extends Mother, Father {
    public static void main(String[] args) {
        Child child = new Child();
        child.speak(); // 메서드 충돌
    }
}
```

- `Father` 클래스와 `Mother` 클래스를 다중 상속하고 있는 `Child` 클래스는 어떤 부모의 `speak()` 메서드를 호출해야하는지 모호함이 발생한다.
- 그렇기 때문에 자바에서는 클래스의 다중 상속을 허용하지 않고 실제로 위 코드는 컴파일 에러가 발생한다.

## 💡자바의 인터페이스 다중 상속

자바에서는 인터페이스의 다중 상속을 지원한다.

인터페이스는 메서드를 정의만 하고 구현체에서 구현해야 하기 때문에 다중 상속으로 인한 메서드 충돌이 발생하지 않는다.

```java
interface Person {
    void speak();
}

interface Father extends Person {
}

interface Mother extends Person {
}

interface Child extends Mother, Father {
}

public class Child1 implements Child {
    @Override
    public void speak() {
        System.out.println("Child1");
    }

    public static void main(String[] args) {
        Child child = new Child1();
        child.speak();
    }
}
```

## 💡자바8의 default 메서드

### 1. 다이아몬드 문제

자바8의 default 메서드로 인해 인터페이스 내부에 구현부를 가진 메서드를 포함할 수 있게 되었다.

인터페이스를 다중 상속했을 때 다이아몬드 문제가 발생할 수 있게 되었다.

```java
interface Person {
    default void speak() {
        System.out.println("Person");
    }
}

interface Father extends Person {
    @Override
    default void speak() {
        System.out.println("Father");
    }
}

interface Mother extends Person {
    @Override
    default void speak() {
        System.out.println("Mother");
    }
}

// 컴파일 에러 발생
interface Child extends Mother, Father {
}
```

### 2. 해결 방법

이러한 경우에는 **인터페이스 혹은 구현체에서 명시적으로 default 메서드를 오버라이딩**하여 문제를 해결할 수 있다.

```java
interface Child extends Mother, Father {
    @Override
    default void speak() {
        System.out.println("Child");
    }
}
```

## 💡결론

- 자바에서 클래스의 다중 상속을 지원하지 않는 이유는 같은 이름의 메서드를 상속받았을 때 어떤 부모의 메서드를 호출해야하는지 모호함이 발생하기 때문이다.
- 자바에서 인터페이스의 다중 상속을 지원하는 이유는 인터페이스에서는 메서드를 정의만하고 구현체에서 구현해서 사용하기 때문이다.
- 자바8의 default 메서드로 인해 인터페이스 다중 상속에서 다이아몬드 문제가 발생하는 경우에는 default 메서드를 재정의해서 문제를 해결한다.

## Reference

- [자바는 왜 다중상속을 지원하지 않을까? (다이아몬드 문제)](https://siyoon210.tistory.com/125)
- [자바에서의 다중상속 문제(a.k.a. Diamond Problem)](https://youngjinmo.github.io/2021/03/diamond-problem/)


# https://github.com/WeeklyStudy/modern-java-in-action/issues/35 인터페이스 사용 시 주의사항에는 무엇이 있을까?

## 💡인터페이스 사용 시 주의사항

1. 인터페이스는 인스턴스화 할 수 없다.
2. 클래스는 인터페이스를 `다중 구현(implements)`할 수 있다.
3. 인터페이스는 인터페이스를 `구현(implements)`할 수 없다.
4. 인터페이스는 인터페이스 `다중 상속(extends)` 을 지원한다.
    - 두 인터페이스에 이름, 파라미터가 같고 리턴 타입이 다른 추상 메서드가 있다. → 두 인터페이스를 다중 상속하면 컴파일 에러가 발생한다.
    - 두 인터페이스에 이름, 파라미터, 리턴 타입이 같은 추상 메서드가 있다. → 두 인터페이스를 다중 상속하는 경우 해당 메서드를 오버라이딩 하면 된다.
5. 인터페이스 변수는 `상수(public static final)` 로 선언해야하고 반드시 선언과 동시에 초기화를 해야한다.
6. 인터페이스에서 특정 키워드가 붙지 않은 모든 메서드는 `public` , `abstract` 이다.
7. 자바8부터 `default 메서드`, `static 메서드`를 지원한다.
8. 자바9부터 `private 메서드`를 지원한다.
9. 인터페이스의 `static 메서드`는 오버라이딩할 수 없다.

## Reference

- [인터페이스(Interface)](https://dahye-jeong.gitbook.io/java/java/undefined/java-interface)
- [인터페이스](https://alkhwa-113.tistory.com/entry/8%EC%A3%BC%EC%B0%A8-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)
- [인터페이스](https://algopoolja.tistory.com/103)
