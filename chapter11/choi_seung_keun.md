# Q. 자바의 직렬화와 Serializable 인터페이스는 무엇일까?

## 직렬화 & 역직렬화

### 직렬화

- 자바 시스템 내부에서 사용되는 Object 또는 Data를 외부 자바 시스템에서도 사용할 수 있도록 byte 형태로 데이터를 변환하는 기술
- JVM 메모리에 상주(힙 또는 스택)되어 있는 객체 데이터를 바이트 형태로 변환 하는 기술

### 역직렬화

- byte 형태로 변환된 데이터를 원래대로 자바 시스템의 Object, Data로 변환하는 기술
- 직렬화된 바이트 형태 데이터를 객체로 변환해서 JVM에 상주시키는 형태

### 바이트 스트림

- 스트림은 입출력을 위한 데이터가 흐르는 통로를 의미
- 자바는 스트림의 기본 단위가 바이트이므로 네트워크, 데이터베이스 전송을 위한 최소 단위인 바이트 스트림으로 변환 처리

## Serializable 인터페이스

Serializable 인터페이스는 어떤 멤버변수나 메서드도 가지고 있지 않은 마커 인터페이스로 해당 인터페이스 구현한 클래스를 생성하면 클래스의 모든 멤버 변수는 직렬화 대상이 된다.

- Serializable 구현한 클래스만 직렬화 될 수 있도록 제한되어 있음
- 객체가 스트림을 통해 직렬화 될 때 모든 변수가 직렬화 됨
- 직렬화 대상에서 제외하고 싶은 것은 transient 키워드를 사용

## 직렬화 사용처

휘발성 있는 캐싱 데이터를 영구 저장 필요 시 사용할 수 있음

### 사용하는 곳

- 서블릿 세션 :
    - 단순히 세션을 서블릿 메모리 위에서 운용하면 직렬화 필요 없음.
    - 세션 데이터 저장, 공유 필요 시(파일 저장, 세션 클러스터링, DB저장) 직렬화 이용
- 캐시 :
    - 객체를 직렬화하여 메모리, 외부 파일에 저장해두었다가 역직렬화하여 사용하는 캐시 데이터로서 사용
    - Redis, Memcached 같은 라이브러리 시스템을 많이 사용
- 자바 RMI(Remote Method Invocation)
    - 원격 시스템  간의 메세지 교환을 위해 사용하는 자바에서 지원하는 기술
    - 메세지에 객체 데이터를 직렬화하여 송신
    - 최근에는 소켓을 이용하기 때문에 안쓰이는 기술

## 직렬화와 JSON

- 직렬화
    - 자바 시스템에서만 사용가능
        - JSON 형태로 객체 데이터를 저장해두면 파이썬, JS 등 에서도 범용적으로 사용 가능
    - 외부파일, 네트워크 통해 클라이언트간에 객체 데이터 주고 받을 때 사용
- JSON
    - 다양한 시스템에서 사용 가능하며 설정 파일이나 데이터 교환을 용도로 범용적으로 사용됨
- 다양한 포맷이 있는데도 직렬화 사용하는 이유
    - 자바 시스템 간의 데이터 교환을 위함
    - 자바 시스템에서 사용하기 최적화되어 있기 때문

## 직렬화 & 역직렬화 사용법

### 직렬화 방법

1. Serializable 인터페이스 구현
    - 직렬화를 위해 java.io.Serializable 인터페이스를 implements 해야 함 ⇒ 아니면 NotSerializableException 예외 발생
    - Serializable 인터페이스는 아무 내용이 없는 마커 인터페이스, 직렬화 고려하여 작성한 클래스인지 판단하는 기준으로 사용 ⇒ 해당 객체를 직렬화하겠다는 의도 표시
    - 예제 코드
        
        ```java
        class Customer implements **Serializable** {
        	int id;
        	String password;
        	String name;
        	int age;
        }
        ```
        
2. ObjectOutputStream 사용하여 직렬화 
    - 객체 직렬화 될 떄 오직 객체의 인스턴스 필드만 저장
    - static 필드, 메서드는 직렬화하여 저장하지 않음
    - 예제 코드
        
        ```java
        public static void main(String[] args) {
        	// 직렬화할 대상 객체
        	Customer customer = new Customer(1, "이름", "비밀번호", "20");
        
        	// 외부 파일명
        	String fileName = "Customer.ser";
        	
        	// 파일 스트림 객체 생성
        	try (
                    FileOutputStream fos = new FileOutputStream(fileName);
                    ObjectOutputStream out = new ObjectOutputStream(fos)
          ) {
        		    // 직렬화 가능 객체를 바이트 스트림으로 변환하고 파일에 저장
                out.writeObject(customer);
          } catch (IOException e) {
                e.printStackTrace();
          }
        }
        ```
        
        - 파일 확장자는 .ser, .txt 등 상관 없으나 직렬화 명시를 위해 .ser, .obj 등 사용
        - 직렬화 되어 저장된 파일 내용은 사람이 읽을 수 없는 문자형태로 저장되어 있음

### 역직렬화 방법

- ObjectInputStream 사용하여 역직렬화
    - 직렬화 대상 객체의 클래스가 외부 클래스라면, 클래스 경로(Class Path) 존재해야 하며, import 된 상태여야 한다.
    - 여러 객체 직렬화하고, 역직렬화 한다면 직렬화 할 때의 순서와 일치해야 함
        
        ⇒ 직렬화 시 객체가 1,2,3 순으로 직렬화 되었다면, 역직렬화도 1,2,3 순으로 진행되어야 함
        
        ⇒ 직렬화할 객체가 많다면  ArrayList와 같은 컬렉션으로 관리하면 좋음
        
    - 예제코드
        
        ```java
        public static void main(String[] args) {
            // 외부 파일명
            String fileName = "Customer.ser";
        
            // 파일 스트림 객체 생성 (try with resource)
            try(
                    FileInputStream fis = new FileInputStream(fileName);
                    ObjectInputStream in = new ObjectInputStream(fis)
            ) {
                // 바이트 스트림 -> 자바 객체로 변환
                **Customer deserializedCustomer = (Customer) in.readObject();**
                System.out.println(deserializedCustomer);
            } catch (IOException | ClassNotFoundException e) {
                e.printStackTrace();
            }
        }
        ```
        
        - 직렬화된 외부 파일(Customer.ser)만 있으면 **생성자(객체 초기화)없이** 바로 인스턴스화하여 사용 가능

## 직렬화의 장단점

### 직렬화 장점

- 자바 고유 기술인 만큼 자바 시스템에서 개발에 최적화
- 자바 레퍼런스 타입에 대해 제약 없이 외부에 내보낼 수 있다. ⇒ 별도의 파싱이 필요 없음
    - JSON 경우 레퍼런스 타입은 단순 파일 포맷으로 한계가 있기 때문에 외부로 보내기 위한 별도의 파싱 필요
    - 직렬화의 경우 자바 시스템이 아닌 다른 시스템에서 사용이 불가능하지만 직렬화 기본 조건만 지키면 복잡한 데이터 구조를 가진 클래스의 객체라도  큰 작업 없이 외부로 보낼 수 있음
    - 역직렬화 통해 읽어들이면 데이터 타입이 자동으로 맞춰지기 때문에 자바 클래스 기능 바로 이용 가능

### 직렬화 단점(문제점)

직렬화는 많은 단점과 위험 요소가 존재함

Serializable 구현해야 할 경우 이득과 비용을 잘 계산해야 하며 시간이 걸려도 JSON 으로 마이그레이션 하는 것을 추천

- 직렬화는 용량이 크다(Data Size 문제)
    - 객체에 저장된 데이터 값 뿐 아니라 타입 정보, 클래스 메타 정보 가지고 있으므로 용량을 많이 차지
    - JSON 과 비교했을 때 직렬화한 데이터의 크기가 2배 이상 차이 남
        - 일반적인 메모리 기반 Cache 에서 Data 저장의 한계가 있음
        - DB, Cache 등 외부 저장 시 장기간 저장하는 정보는 직렬화 지양
- 역직렬화의 위험성
    - 역직렬화를 위한 ObjectInputStream의 `readObject()` 호출 시 classpath 안의 모든 타입의 객체를 생성
        
        ⇒ 타입 객체안의 모든 코드 수행할 수 있게 되기 때문에 프로그램 코드 전체가 공격 범위에 들어간다.
        
    - 외부 전송 과정에서 중간에서 파일을 가로채 바이트 내용을 조작하여 역직렬화시 인스턴스에 위험한 값 대입시키는 공격 가능
        - 생성자 없이 인스턴스화 가능하기 때문
        - 신뢰할 수 없는 데이터를 절대 역직렬화하면 안됨 ⇒ 아무것도 역직렬화 하지 않음
- 릴리즈 후에 수정이 어려움
    - Serializable 구현하게 되면 클래스 내부 구현 수정 시 원래의 직렬화 형태와 달라짐
        
        ⇒ 해당 객체의 유지보수는 직렬화에 묶이게 됨
        
- 클래스 캡슐화 깨짐
    - 직렬화할 클래스에 private 멤버가 있어도 그대로 외부로 노출됨
        
        ⇒ Serializable 구현 시 직렬화 형태가 공개 API 됨
        
    - 직렬화에서 제외하려면 transient 설정 해야 함
- 버그와 보안에 취약
    - 역직렬화는 자바의 생성자를 통해 객체를 생성하는 매커니즘을 우회하여 객체를 바로 생성하므로 허가되지 않는 접근에도 무시하고 객체가 생성 됨
    - 직렬화 프록시 패턴으로 극복 가능
- 내부 클래스 직렬화 구현하면 안됨
    - 내부 클래스의 직렬화 형태는 불분명하기 때문에 Serializable 구현하면 안됨
    - 정적 내부 클래스는 Serializable 구현해도 상관없음
    

### 참고자료
- [자바 직렬화(Serializable) - 완벽 마스터하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A7%81%EB%A0%AC%ED%99%94Serializable-%EC%99%84%EB%B2%BD-%EB%A7%88%EC%8A%A4%ED%84%B0%ED%95%98%EA%B8%B0)
- [Java의 직렬화(Serialize)란?](https://go-coding.tistory.com/101)
- [Serializable 인터페이스에 대하여](https://1-7171771.tistory.com/116)

</br>

# Q. Optional 의 orElse() vs orElseGet()

## 공통점과 차이점

### 공통점

- Optional 에서 기본 값을 통해 null을 처리의 도움을 준다.

### 차이점

- 메서드 형태
    
    ```java
    public T orElse(T other) {
    	return value != null ? value : other;
    }
    
    public T orElseGet(Supplier<? extends T> other) {
    	return value != null ? value : other.get();
    }
    ```
    
    - orElse는 T클래스를 인수로 받으며 모든 매개 변수 사용
    - orElseGet은 T클래스 상속받은 하위 클래스를 return 해주는 Supplier 인터페이스 인수로 받음 ⇒ get 호출하여 결과를 리턴하는 역할
- orElse()
    - 메소드를 인수로 받지 않고 값을 인수로 받음
    - 메소드를 인수로 넘길 경우 메서드는 단순 호출 메서드로 사용되고, 메서드의 결과 값을 orElse()의 인수로 할당
        
        ⇒ 즉 Optional 값 상관없이 메서드 실행하고 해당 결과를 인수로 넘김
        
- orElseGet()
    - orElseGet()은 메서드를 인수로 받음
        
        ⇒ 따라서 메서드의 실행 여부는 Optional의 값이 null 일 때만 실행
        

### 정리

- orElse()와 .orElseGet()은 null이든 아니든 모두 인자로 받은 값들을 호출
- orElse()를 사용할 때, 메서드를 인자로 가지면 null이 아니어도 호출
    - 반환값은 호출한 메서드의 반환값으로 바뀌지 않음
- orElseGet()은 null일 때만 호출
    - supply()를 사용하므로 Lazy-evaluation으로 동작한다.
    - 매번 호출하지 않기 때문에 성능상으로 orElseGet()이 조금 더 좋음

### 참고자료

- [[Java] Optional – orElse() vs orElseGet() 차이점 알고 쓰자.](https://kdhyo98.tistory.com/40)
- [Optional.orElse() vs .orElseGet()](https://giron.tistory.com/153)

</br>

# Q. Optional 장단점은 무엇일까?

## Optional 이란

값이 존재할 수도, 존재하지 않을 수도 있는 값을 포장한 객체

- Optional<T>는 null이 올 수 있는 값을 감싸는 Wrapper 클래스로, 참조하더라도 NPE가 발생하지 않도록 도움을 줌
- Optional 클래스는 value에 값을 저장하기 때문에 값이 null이더라도 바로 NPE가 발생하지 않으며, 클래스이기 때문에 각종 메소드를 제공
- Java8 추가 됨

## **Optional 의 장점**

- 명시적으로 변수에 대한 null 가능성을 표현 가능
- 직접 null 체크하지 않아도 됨
- Null Point Exception 이 발생할 가능성이 있는 값을 직접 다룰 필요가 없음

## **Optional 의 단점**

- Wrapper 클래스이기 때문에 두 개의 참조를 가지므로 생성 비용이 비쌈
- 직렬화 불가능하기 때문에 Optional을 클래스의 인스턴스 필드로 사용하면 안됨
- 필드로 사용하기 위해 고안된 것이 아니기 때문에 **값을 반환하는 용도로 사용**해야 한다.

## 정리

- 무분별한 Optional 사용은 오히려 좋지 않음
- Optional 을 정확하게 이해하고 사용한다면 명시적이고 가독성 있는 코드 작성 가능

## 참고자료
- [Optional 과 null 에 대해](https://tecoble.techcourse.co.kr/post/2021-06-20-optional-vs-null/)
- [[Java] Optional이란? 개념과 사용법](https://frtt0608.tistory.com/152)
