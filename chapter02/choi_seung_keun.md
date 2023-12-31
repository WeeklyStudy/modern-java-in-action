## 답변

# Q.  전략 패턴의 장단점은 무엇이고,  어떠한 경우에 적용하면 좋을까?

### 전략패턴이란?

실행 중에 알고리즘을 선택(전략을  변경)할 수  있게 하는 행위 소프트웨어 디자인 패턴 

- 전략은 어떤 목적을 달성하기 위한 일의 수행 방식, 문제 해결을 위한 알고리즘으로 이해할 수 있음
- 특정 기능을 수행하는 여러 알고리즘이 존재 및 적용 가능할 때  알고리즘을  별도로 분리하여 설계하고 전략에 따라 이들을 교체하여 동일한 기능을 다른 방법으로 수행할  수 있도록 함

### 전략 패턴의 장단점

장점

- 새로운 전략(알고리즘)을 추가할 때 기존의 코드를 변경하지 않는다.
    
    ⇒  확장에는 열려있고, 수정에는 닫혀있음  ⇒  개방 폐쇄의  원칙(OCP)을 따름
    
- 추상화에 의존하고 있고 전략만 교체하면 되기 때문에 요구 사항 변경에 좋다.

단점

- 전략(알고리즘)이 다양하지 않거나 요구사항의 변경이 많지 않은 경우 굳이 전략 패턴을 적용할 필요가 없다.
- 전략이 늘어날  때마다 구현해야 할 클래스와 복잡도가 늘어난다.
    
    ⇒ 재사용되지 않는 클래스는 람다식을 이용하여 클래스 생성하지 않고 만들 수 있음
    

### 어떠한 경우에 적용하면 좋을지(개인적인 생각)

- 요구 사항의 변경이나 확장이 자주 일어나는 곳에  적용하면 좋을 것이다.
- 다양한 전략을 가지고 있고 상황에 따라 정책을 선택해야 할 때 좋을 것이다.
    
    이커머스 서비스처럼 다양한 결재 방법, 할인 정책을 가지고 있고  회원이 선택하는 방법(전략)에 따라 다른 로직을 실행하는 경우
    
- 아직 정해지지 않은 정책에 대해 적용하면 좋을 것이다.
    
    DBMS 같은 세부적인 저장소나 정책에 대해 정해지지 않았을 때 임시 전략을 사용해 개발을 진행하고 추후 정책이 정해지면  교체하는 경우 
    

### 참고 자료

- [전략 패턴 (Strategy Pattern) 이란, 개념 및 예시](https://developercc.tistory.com/18)
- [디자인 패턴 : 전략패턴이란?](https://velog.io/@kyle/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%A0%84%EB%9E%B5%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80)

</br>

# Q.  인터페이스와 추상 클래스의 차이점은 무엇이고,  어떤 경우에 인터페이스를 사용하는가?

### 추상 클래스

하나 이상의 추상 메서드를 가지는 클래스

- 추상 메서드는를 메서드 선언만 작성하고 구현부는 작성하지 않으며 상속 받은 하위 클래스에서 추상 메서드 구현을 강제  함
- 추상 메서드를 포함하는 클래스는 반드시 추상 클래스여야 함
- 다중 상속  불가능

### 인터페이스

상수와 추상 메서드의 집합

- 추상 클래스보다 추상화 정도가  높고 구현부가 있는 일반 메서드와 일반 멤버 변수를 가질 수 없음
- 모든 멤버 변수 상수로 선언(public  static final)
- 다중 상속  가능

### 공통점

- 선언만 있고 구현이 없는  추상 메서드를  가지고 있다.
- new 통한 객체  생성 불가능, 상속 받은 하위 클래스에서 추상 메서드 구현  강제

### 차이점

- 외형적 차이
    - 추상 클래스  :
        - extends 사용
        - 다중 상속 불가능
        - 일반 변수, 생성자, 일반 메서드, 추상 메서드 가짐
    - 인터페이스  :
        - implements 사용
        - 다중 상속 가능
        - 상수와 추상 메서드만 가짐
- **`목적의 차이 :`**
    - **추상 클래스 :  상위 클래스 상속받아 그 기능을 이용하거나  확장하기 위함**
    - **인터페이스 :  함수의 껍데기의 구현을 강제하여 인터페이스를 구현한  객체에 대해  동일한 동작을 보장하기 위함**

### 참고자료

- [자바 추상 클래스와 인터페이스의 차이점 이해하기](https://wildeveloperetrain.tistory.com/112)
- [자바의 추상 클래스와 인터페이스](https://brunch.co.kr/@kd4/6)

</br>

# Q. Comparator와 Comparable의 각  특징과  차이점은 무엇일까?

### Comparable  이란?
- 인터페이스이며, 하나의 메서드인 compareTo(T o) 메서드를 가지고 있음
- Comparable 사용 시 compareTo(T o) 메소드를 재정의(구현)해야 함

![image](https://github.com/WeeklyStudy/modern-java-in-action/assets/77659341/85e57ccf-b5a3-4b52-b5a9-79f5c4c2eaef)

### Comparator 이란?

- 인터페이스이며, 정적 메서드, 추상 메서드, 디폴트 메서드 등 다양한 메서드를 가지고 있음
    
    ⇒ Java8 부터는 인터페이스에서 일반 메서드를 구현할 수 있음
    
    ⇒ 대부분이  디폴트 메서드, 정적  메서드
    
- Comparator 사용 시 compare(T  o1, T o2) 메서드를 구현해야 한다.

![image](https://github.com/WeeklyStudy/modern-java-in-action/assets/77659341/d502be17-f233-429b-875e-c319fb9650ab)

### 공통점

- 객체를 비교할 수 있도록 함
    
    기본 자료형 비교  VS 객체비교
    
    - 기본 자료형 타입: 비교의 기준이 명확하기 때문에 부등호등을  이용한 변수 비교 가능
    - 객체  비교: 사용자가 비교의 기준을 정해주지 않으면 판단하는 기준을 알 수 없음
        
        **⇒ 따라서 객체 비교를 위해서 비교 기준을  정해주는 작업인 추상 클래스를 구현해야 한다.**
        
    
### 차이점

- Comparable
    - 비교를 해주는 CompareTo 메서드는 하나의 객체를 파라미터로 받는다
    - 비교/정렬의 대상이 되는 객체 내부에 비교/정렬의  기준을 구현하고 파라미터로 오는 객체와  비교를 한다.
    - 예시코드
        - 비교 대상 클래스(Student), 구현체(Student)

          ```jsx
            class Student implments Comparable<Student> {
            	int classNumber;
            	int age;
            	int score;
            
            	Student(int className, int age, int score) {
            		this.classNumber = classNumber;
            		this.age = age;
            		this.score = score;
            	}
            
            	@Override
            	public int compareTo(Student o){
            		return this.age - o.age;
            	}
            }
            ```
            
        - 실행 클래스
            
            ```java
            pubilc class Example {
            	public static void main(String[] args) {
            		ArrayList<Student> classRoom = new ArrayList<>();
            	
            		students.add(new Student(1, 20, 100));
            		students.add(new Student(2, 21, 90));
            		students.add(new Student(3, 22, 80));
            		students.add(new Student(4, 23, 70));
            
            		Collections.sort(students);	int classNumber;
            	int age;
            	int score;
            }
            ```
              
- Comparator
    - 비교를 해주는 Compare 메서드는 두 개의  객체를  파라미터로 받는다.
    - Compartor 구현체는 해당 클래스가 비교/정렬의 기준이 되고, 파라미터로 오는 두 개의 객체를 해당 기준에 따라 비교한다.
        - Comparator 구현하여  다양한 정렬 기준을 생성 가능
        - 전략 패턴을 사용하여 다양한 정렬 기준을 쉽게 정렬 변경/교체 가능
    - 예시코드
        - 비교 대상 클래스(Student), 구현체(AgeSort, ScoreSort)
            
            ```java
            class Student {
            	int classNumber;
            	int age;
            	int score;
            
            	Student(int classNumber, int age, int score) {
            		this.classNumber = classNumber;
            		this.age = age;
            		this.score = score;
            	}
            	
            	// 각 필드에 대한 getter
            	public int getClassNumber() { return this.classNumber };
            	public int getAge() { return this.age };
            	public int getScore() { return this.score };
            }
            
            class AgeSort implements Comparator<Student> {
            	@Override
            	public int compare(Student o1, Student o2) {
            		return o1.getAge() - o2.getAge();
            	}
            }
            
            class ScoreSort implements Comparator<Student> {
            	@Override
            	public int compare(Student o1, Student o2) {
            		return o1.getScore() - o2.getScore();
            	}
            }
            ```
            
        - 실행 클래스
            
            ```java
            public class Example {
            	public static void main(String[] args) {
            		ArrayList<Student> students = new ArrayList<>();
            
            		students.add(new Student(1, 20, 100));
            		students.add(new Student(2, 21, 90));
            		students.add(new Student(3, 22, 80));
            		students.add(new Student(4, 23, 70));
            		
            		// 학생 나이에 따른 정렬
            		AgeSort ageSort = new AgeSort();
            		Collections.sort(students, ageSort);
            
            		// 학생의 점수에 따른 정렬
            		ScoreSort scoreSort = new ScoreSort();
            		Collections.sort(students, scoreSort);
            	}
            }
            ```
            
    
### 참고 자료
- **[[Comparator 와 Comparable - JAVA](https://dding9code.tistory.com/68)](https://dding9code.tistory.com/68)**
- ****[[Comparable, Comparator 차이](https://velog.io/@ovan/Comparable-Comparator)](https://velog.io/@ovan/Comparable-Comparator)****
- **[[[Java] Comparable과 Comparator의 차이](https://sunrise-min.tistory.com/entry/Java-Comparable%EA%B3%BC-Comparator%EC%9D%98-%EC%B0%A8%EC%9D%B4)](https://sunrise-min.tistory.com/entry/Java-Comparable%EA%B3%BC-Comparator%EC%9D%98-%EC%B0%A8%EC%9D%B4)**
