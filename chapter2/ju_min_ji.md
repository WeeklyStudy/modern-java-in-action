## 문제 1

변화에 유연하게 대처하기 위해 `동작 파라미터화` 를 이용했다.
메서드가 캡슐화한 전략을 파라미터로 받아 런타임에 알고리즘을 선택할 수 있게 되었다. 이를 `전략 디자인 패턴` 이라고 부른다고 한다.

예제를 통해 **전략 패턴이란 무엇인지** 이해하고 **전략 패턴의 장단점**과 **어떠한 경우에 전략 패턴을 적용하면 좋을지** 생각해보자.

## 답변 1

![image](https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/b117a18c-db82-4542-9c68-7f578705f326)

### 1. 전략 패턴이란?

한 유형의 알고리즘을 보유한 상태에서 런타임에 적절한 알고리즘을 선택하는 기법이다.

### 2. 전략 패턴 장단점
- 장점:
  - 유연성 및 확장성
  - 코드 재사용성
  - 결합도 감소
- 단점:
  - 복잡성 증가
     - 클래스로 분리한 각 전략들이 어느 상황에 사용되어야 할 지 알고 있어야 하고, 어떤 전략 객체에서는 사용하지 않는 메서드들 역시 전략 인터페이스에 정의해주어야 한다.

### 3. 전략 패턴을 도입하면 좋은 경우
새로운 요구사항 등에 의하여 객체의 행위를 동적으로 바꾸고 싶은 경우, 직접 행위를 수정하지 않고 전략을 바꿔주기만 함으로써 행위를 유연하게 확장할 수 있다.
따라서, 객체지향 원칙 중 OCP를 준수하기 위해 사용하는 패턴이기도 하다.

#### 💡 OCP(Open Close Principle, 개방 폐쇄 원칙)란? 
> - OCP는 객체지향 5원칙(SOLID 원칙) 중 두번째 원칙에 해당된다.
> - 변경에 대해서는 닫혀있지만, 확장에 대해서는 열려있어야 한다는 원칙이다. 즉, 기존의 코드를 변경하지 않으면서 기능을 추가할 수 있도록 설계해야 한다.
> - 객체를 직접 수정하는 일은 객체지향적인 설계도 아닐 뿐더러, 유지보수 비용이 증가될 수 있기 때문이다.
> - 로버트 C.마틴은 OCP를 가능케 하는 주요 메커니즘은 **추상화**와 **다형성**이라고 설명한다.
>

### Reference

- [[디자인패턴] 전략 패턴 ( Strategy Pattern )](https://victorydntmd.tistory.com/292)
- [테코톡정리 - OCP와 전략패턴](https://velog.io/@gudnr1451/%ED%85%8C%EC%BD%94%ED%86%A1%EC%A0%95%EB%A6%AC-OCP%EC%99%80-%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4)
- [SOLID 개방폐쇄의 원칙 OCP](https://www.youtube.com/watch?v=fJcZML8ZQAE)
- [저는 전략 패턴이 처음이라니까요](https://joel-dev.site/75)

## 문제 2

2장에서는 `인터페이스`를 구현한 클래스를 파라미터로 받아 동작 파라미터화하는 방법을 배울 수 있었다. 
인터페이스는 기본적인 개념이긴 하지만, 2장 예제 코드에서 많이 활용된 만큼 인터페이스의 개념을 명확하게 이해하고 가면 좋을 것 같다. 

따라서, 인터페이스와 비슷한 역할을 하는 **추상 클래스와의 차이점**을 통해 **인터페이스의 특징**을 이해하고, **어떤 경우에 인터페이스를 사용하는지** 알아보자.
## 답변 2

### 1. 인터페이스와 추상 클래스 차이점
- 추상성
    - 인터페이스가 기본 설계도라면, 추상 클래스는 미완성 설계도라고 볼 수 있다.
    - 즉, 인터페이스는 모든 메서드가 추상 메서드인 경우를 뜻한다.
    - 따라서, 추상 클래스 내 추상 메서드는 abstract 예약어를 사용하지만, 인터페이스 내의 모든 메서드는 추상메서드로 간주되기 때문에 abstract를 (굳이) 적지 않는다.
    - 이 때문에 인터페이스는 추상 클래스보다 한 단계 더 추상화 된 클래스로 간주된다.
- 다중 상속
    - 엄밀히 말하면, 다중 구현(implements)이다.
    - 인터페이스에는 추상 메서드로 구성되어 중간 과정에 구현체가 없기 때문에, 어떤 부모로부터 상속(구현)되었는지 알 필요가 없기 때문이다.
    - OOP 에서는 다형성을 중요시하지만, 클래스의 다중 상속은 제한하여 자유로운 다형성 부여를 할 수 없었다.
    - 하지만, 인터페이스를 통해 다소 제한된 다중 상속(구현)을 허용하고 있다.

 따라서 인터페이스는 일반화된 계약(Contract)을 제공하는 `기본 설계도`로 사용되며, Java와 같이 클래스의 다중 상속을 지원하지 않는 환경에서 `클래스 간의 매개체` 역할을 한다. 이러한 특성으로 인터페이스는 추상 클래스와 구별되는 특징을 가지게 된다.

### 2. 인터페이스를 언제 사용해야 하는가

- 클래스 간의 공통된 동작을 정의하면서도, 객체(클래스)에 따라 다른 동작을 구현하여 추상 클래스보다 `다형성`을 부여하고 싶을 때 유용하다. 
  - 이를 통해 선언과 구현을 분리하여, 개발자는 기능 구현 즉 비즈니스 로직에만 집중할 수 있다.
- 또한, 객체지향 5원칙 SOLID 중 하니인 `DIP`를 지키기 위해 사용된다.
  
  - DIP를 위반한 코드
    ```java
    public class MemberService{
	  private MemberRepository memberRepository = new MemoryMemberRepository();
      
      public void save(Member member){
          memberRepository.save(member);
       }
    }
    ```
     - 인터페이스와 추상 클래스는 추상화된 개념으로 직접적으로 인스턴스화될 수 없기 때문에 구현체 클래스에서 해당 추상화 개념을 구현한 후 객체를 생성할 수 있다.
     - 위 코드에서는 MemberRepository의 구현체인 MemoryMemberRepository를 선언해서 사용하고 있기 때문에 DIP를 위반하고 있다.
  
  - DIP를 준수한 코드
    ```java
    @RequiredArgsConstructor
    @Service
    public class MemberService{
       private final MemberRepository memberRepository;
      
   	   public void save(Member member){
         memberRepository.save(member);
       }
    } 
    ```
    - 생성자의 final 키워드와 @RequiredArgsConstructor 어노테이션을 통해 MemberRepository 인터페이스를 주입받고 있다.
    - 위 코드에서는 MemberService가 MemberRepository 인터페이스에 의존하기 때문에 DIP를 준수하고 있다.

#### 💡 DIP(Dependency Inversion Principle, 의존성 역전 원칙)란?

> - DIP는 고수준 모듈은 저수준 모듈에 의존해서는 안 된다는 원칙이다. <br>
> - UML에서 추상화의 방향은 저수준 → 고수준(실제로 사용하는 것과 근접해있는 것)으로 되어있는데, 저수준 모듈이 변경되더라도 고수준 모듈에 영향을 끼치면 안된다는 의미이다.
> - 고수준이 저수준에 의존하지 않고, 저수준이 고수준에 의존하게 하는 것은 **추상화**를 통해 실현하라는 원칙인데 이 때 사용되는 추상화가 인터페이스이다. 
> - 즉, DIP를 달리 말하면 객체 참조 시, 구현체를 참조하지말고 인터페이스를 참조하라는 원칙이라고도 할 수 있다.

### 3. 결론
- 추상클래스보다 유연하게 기능을 확장시키고 싶을 때, 인터페이스를 사용한다.
- 구현체 대신 인터페이스를 사용하여 하위모듈의 변경이 상위모듈의 변경을 요구하는 위계 관계를 단절시킴으로써(DIP) 그 자체로 재사용되고 확장성도 보장 받을 수 있다.

### Reference

- [[Java] 자바의 다중상속 (feat. 인터페이스와 추상클래스)](https://junior-datalist.tistory.com/213)
- [[JAVA] 추상클래스 VS 인터페이스 왜 사용할까? 차이점, 예제로 확인 :: 마이자몽](https://myjamong.tistory.com/150)
- [Java의 다중 상속 문제와 인터페이스](https://velog.io/@tsi0521/Java의-다중-상속-문제와-인터페이스#3-그렇다면-인터페이스-다중-구현은)
- [SOLID 원칙 - (5) 의존 역전 원칙 (DIP)](https://4z7l.github.io/2021/06/18/SOLID_DIP.html)
- [[Spring] 스프링의 객체지향](https://velog.io/@byunsw4/스프링-핵심-원리-1#2-dip)
- [[Java] SOLID (SRP, OCP, LSP, ISP, DIP)](https://velog.io/@hanblueblue/Java-SOLID-SRP-OCP-LSP-ISP-DIP#5-dip)

## 문제 3

해당 목차에서는 동작 파라미터화를 설명하며 Comparator 를 이용하여 정렬하는 방법을 예제 코드로 보여준다.

자바를 사용하며 Comparable, Comparator 를 자주 접했는데 해당 인터페이스는 어떠한 특징과 차이점을 가지고 있을까?

## 답변 3

### 1. Comparable이란?

```
java.lang.Comparable< T>
```

- 공식문서에 의하면 Comparable은 클래스의 자연 순서, 즉 기본 정렬 기준이 되는 메서드를 정의하는 인터페이스이다. (This ordering is referred to as the class's natural ordering, and the class's compareTo method is referred to as its natural comparison method.)
- Comparable 인터페이스를 구현한 클래스는 `compareTo()` 메서드를 구현해야 한다. `compareTo`는 자기 자신과 매개변수를 비교하는 Comparable 메서드로, 클래스의 기본 정렬 기준을 정의하기 때문에 반드시 구현해야 한다.

### 2. Comparator이란?

```
java.util.Comparator< T>
```

- Comparator는 객체의 비교 동작을 사용자(혹은 개발자)가 직접 정의하고 싶을 때 사용된다. 즉, 객체의 기본 순서가 아닌 다른 기준으로 자유롭게 정렬하고 싶을 때 유용하다.
- Comparator 인터페이스를 구현한 클래스는 `compare()` 메서드를 구현해야 한다. `compare()`은 두 매개변수 객체를 비교하는 Comparator메서드로, 정렬 기준 재정의 시 오버라이드하여 사용하는 메서드이다.

### 3. Comparable과 Comparator 주요 차이점

따라서, Comparable은 객체의 기본 순서를 정의할 때 사용하고, Comparator는 객체를 특정 기준에 따라 정렬하고 싶을 때 사용한다. Comparable은 클래스 내에서 구현되고, Comparator는 외부에서 객체들을 비교할 때 유연하게 사용할 수 있다.

### Reference

- [자바 [JAVA] - Comparable 과 Comparator의 이해](https://st-lab.tistory.com/243)
- [JAVA 8 공식문서](https://docs.oracle.com/javase/8/docs/api/java/lang/Comparable.html)
