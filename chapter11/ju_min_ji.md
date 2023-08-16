## Q1. **Optional의 장단점은 무엇일까?**

### 1. Optional이란?

- Optional<T> 클래스는 Integer나 Double 클래스처럼 'T'타입의 객체를 포장해 주는 래퍼 클래스(Wrapper class)이다.
- Integer, Double 클래스 등이 Primitive Type을 감싸기 위한 클래스라면, Optionl은 Null이 될 수 있는 값을 감싸는 클래스이다.

### 2. Optional 도입 배경

- 변수값에 null이 들어오거나, 이로 인해 NullPointerException이 발생할 수 있는 상황에서 null 처리하는 방법을 개선하고 가독성을 높이기 위한 도구로 도입되었다
- **Optional의 역할**은 더욱 명확한 **메서드 시그니처**를 통해 더 **이해하기 쉬운 API를 설계**하도록 돕는 것이다.

> 💡 **NPE(NullPointerException)를 조심해야 하는 이유**
> 
> 
>  null을 반환한 근본적인 부분에서 예외가 발생하는 것이 아니라, **원인과 상관 없는 부분에서** 그 값을 사용하려할 때 예외가 발생하기 때문에 **발생 지점을 추적하기 번거롭다**.
> 

### 3. Optional 장점

- null의 의미론적인 모호함 제거 **→ 가독성**을 ****향상시킬 수 있다.
    - `Optional.empty()`: 값이 없다.
    - `Optional.of(value)`: 값이 존재한다. (null이 들어오면 NullPointerException을 던진다.)
    - `Optional.ofNullable(value)`: 값이 존재할 수 있다. (null이 들어오면 빈 옵셔널을 생성한다.)
- **상황에 맞는 예외**를 던질 수 있다.
    - `Optional.orElseThrow()` 메서드: Optional이 비어있을 때 예외를 발생시킨다는 점에서 get 메서드와 비슷하지만 이 메서드는 ****발생시킬 예외 종류를 선택할 수 있다.
- **메서드 체이닝**을 통해 연속적인 작업을 수행하기 용이하다.
    
    ```java
    public String getCityOfMemberFromOrder(Order order) {
    	return Optional.ofNullable(order)
    			.map(Order::getMember)
    			.map(Member::getAddress)
    			.map(Address::getCity)
    			.orElse("Seoul");
    }
    ```
    

### 4. Optional 단점

- NullPointerException 대신 **NoSuchElementException**가 발생할 수 있다.
    - Null-Safe하기 위해 Optional을 사용하였는데, 값의 존재 여부를 판단하지 않고 접근한다면  NoSuchElementException을 발생시키기 때문에 중첩된 null 체크와 크게 다르지 않다.
        - `Optional.get()` : 값이 없으면 NoSuchElementException을 발생시킨다.
- 도메인 모델에 Optional을 사용했을 때 데이터를 **직렬화**할 수 없다.
    - 직렬화 모델이 필요하다면 아래 코드처럼 인스턴스 필드에 Optional<T>로 선언하는 대신, **Optional로 값을 반환받을 수 있는 메서드**를 추가하는 방식을 권장된다.
        
        ```java
        public class Person {
        	private Car car;
        	public Optional<Car> getCarAsOptional() {
        				return Optional.ofNullable(car); // null이면 빈 Optional 객체 반환
        ```
        
- 시공간적 비용이 증가한다.
    - 박싱된 기본 타입 옵셔널은 값을 두 겹이나 감싼 형태이기 때문에 기본 타입 자체보다 무겁고 접근하는 데에 시간이 더 걸린다.
    - 어떤 글에서는 Optional을 사용하면 단순 값을 사용했을 때보다 메모리 사용량이 4배 정도 증가한다고 한다.

### 5. Optional 사용법 가이드

- Optional 변수에 null을 할당하지 말자.
- 단순히 값을 얻으려는 목적으로만 Optional을 사용하지 말자.
    - 단순히 값을 얻는 경우에는 삼항 연산자를 이용하는 것이 낫다.
        
        ```java
        // AVOID
        public String findUserName(long id) {
            String name = ... ;
            
            return Optional.ofNullable(name).orElse("Default");
        }
        
        // PREFER
        public String findUserName(long id) {
            String name = ... ;
            
            return name == null 
              ? "Default" 
              : name;
        }
        ```
        
- 값이 없으면 `orElseThrow()`를 통해 java.util.NoSuchElementException 예외를 발생시키자.
    
    ```java
    // AVOID
    public String findUserStatus(long id) {
    
        Optional<String> status = ... ; // prone to return an empty Optional
    
        if (status.isPresent()) {
            return status.get();
        } else {
            throw new NoSuchElementException();        
        }
    }
    
    // PREFER
    public String findUserStatus(long id) {
    
        Optional<String> status = ... ; // prone to return an empty Optional
    
        return status.orElseThrow();
    }
    ```
    
- 생성자, 수정자, 메소드 파라미터 등으로 Optional을 넘기지 말자.
    - 넘겨온 파라미터를 위해 자체 null체크도 추가로 해주어야 하고, 코드도 복잡해지는 등 상당히 번거로워지기 때문에 반환값으로만 사용하는 것이 권장된다.
- Collection의 경우 Optional이 아닌 빈 Collection을 사용하자.
    - 굳이 Optional로 감싸지 말고 빈 컬렉션을 사용하는 것이 가볍고, 깔끔하게 처리할 수 있다.

### 6. 결론

- Optional은 **반환 타입**으로써, **에러가 발생할 수 있는 경우에 결과 없음을 명확히 드러내기 위해** 만들어졌으며, Stream API와 결합되어 유연한 **체이닝 api**를 만들기 위해 탄생한 것이다.
- Optional을 잘못 사용하는 것은 비용은 증가시키는 반면에 코드 품질은 오히려 악화시킨다. 특히, **성능**이 중요한 상황에서는 옵셔널 사용이 맞지 않을 수 있다.
- 따라서, **결과가 없을 수 있으며**, 클라이언트가 이 상황을 **특별하게 처리**(특수한 동작, 기본값 설정, 예외 처리 등)해야하는 경우에만 Optional을 **반환**하게 해야 한다.

### References

- [[Java] 언제 Optional을 사용해야 하는가? 올바른 Optional 사용법 가이드 - (2/2)](https://mangkyu.tistory.com/203)
- [What are the pros and cons of using Optional class from Java 8 to manage null value in Java programs?](https://www.quora.com/What-are-the-pros-and-cons-of-using-Optional-class-from-Java-8-to-manage-null-value-in-Java-programs)
- [Optional 클래스](http://www.tcpschool.com/java/java_stream_optional)
- [자바8 Optional 3부: Optional을 Optional답게](https://www.daleseo.com/java8-optional-effective/)
- [아이템55. 옵셔널(Optional)](https://alexander96.tistory.com/32)
- [[Effective Java] 8장 메서드 ( null 체크, Optional )](https://umbum.dev/1029)

## Q2. **Optional의 orElse() vs orElseGet()**

### 1. orElse()와 orElseGet() 차이

- `orElse()`와 `orElseGet()`은 둘 다 ****, 값이 없을 경우 Optional 객체에서 값을 가져오는 메서드이지만, 상황에 맞게 적합한 메서드를 올바르게 사용해야 한다.

```java
/**
 * Return the value if present, otherwise return {@code other}.
 *
 * @param other the value to be returned if there is no value present, may
 * be null
 * @return the value, if present, otherwise {@code other}
 */
public T orElse(T other) {
  return value != null ? value : other;
}

/**
  * Return the value if present, otherwise invoke {@code other} and return
  * the result of that invocation.
  *
  * @param other a {@code Supplier} whose result is returned if no value
  * is present
  * @return the value if present otherwise the result of {@code other.get()}
  * @throws NullPointerException if value is not present and {@code other} is
  * null
  */
public T orElseGet(Supplier<? extends T> other) {
  return value != null ? value : other.get();
}
```

- `orElse()` :
    - 메서드 시그니처: `T orElse(T other)`
    - Optional이 값을 포함하지 않을 때 기본값 제공하는 메서드이다.
    - 이미 준비되어 있는 값을 사용하기 때문에, 주로 객체가 비어있어도 상관없는 기본값이나 상수를 제공할 때 사용된다.
- `orElseGet()` :
    - 메서드 시그니처:  `T orElseGet(Supplier<? extends T> supplier)`
    - 런타임에 고정되지 않는 값을 지연 로딩(Lazy Loading)할 수 있다.
    - `Optional` 객체가 비어있을 때 기본값을 생성하는 람다 표현식(`Supplier`)을 제공한다.

### 2. orElse() 주의점

- `orElse()`와 `orElseGet()`은 null이든 아니든 모두 인자로 받은 값들을 호출한다.
- 그러나, `orElse()` 는 **항상 인자로 전달된 값을 평가(evaluate, 계산)**하기 때문에, `orElse()`의 인자로 메서드가 전달되면 인자로 가지면 불필요한 연산을 수행하거나 예외를 발생시킬 수 있다.
- 반면, `orElseGet()` 은 **Optional이 비어있을 때에만 람다식이 평가**되므로 불필요한 연산을 피하고 안전하게 사용할 수 있다.
- 예외가 발생할 수 있는 코드 예제 - `orElse()`
    
    ```java
    public List<TabMenu> getTabMenu (UserInfo userInfo) {
        return List<TabMenu> tabMenus = Optional.ofNullable(getDBData(userInfo))
          .orElse(fallback());
    }
    
    private List<TabMenu> fallback() {
      // fallback yml 데이터를 읽어옴
    }
    ```
    
    - `getDBData(userInfo)`에서 데이터가 null이 아닐 경우에도 `fallback()` 메서드가 호출되므로, `fallback()` 내부에서 insert나 update와 같은 데이터베이스 조작을 수행하면 불필요한 데이터 조작이 발생할 수 있다.
- 위 코드를 문제가 발생하지 않도록 수정하기 - `orElseGet()`
    
    ```java
    public List<TabMenu> getTabMenu(UserInfo userInfo) {
        return Optional.ofNullable(getDBData(userInfo))
                       .orElseGet(this::fallback);
    }
    
    private List<TabMenu> fallback() {
        // fallback yml 데이터를 읽어옴
    }
    ```
    
    - `orElseGet()`을 사용하여 필요한 경우에만 데이터 조작이 발생하도록 제어할 수 있다.

### 3. **Optional의 orElse() vs orElseGet() 용도**

- 단순 기본 값일 때는 `orElse()` 를 사용하는 것이 적합하고, 이 외에 비용이 큰 연산이나 동적인 생성이 필요한 경우에`orElseGet()`을 사용하면 효율적으로 사용할 수 있다.
    - 특히, JPA 등을 사용하여 Entity가 변경이 되는 부분이 캡슐화되면 찾기가 어렵기 때문에 잘 구별해서 사용해야 겠다.

### Reference

- https://giron.tistory.com/153
- https://ysjune.github.io/posts/java/orelsenorelseget/

## Q3. **자바의 직렬화(Serialization)와 Serializable 인터페이스는 무엇일까?**

### 1. 자바의 직렬화**(Serialization)**

- 목적:
    - 자바 시스템 내부에서 사용되는 Object 또는 Data를 **외부의 자바 시스템에서도 사용할 수 있도록**
- 프로세스:
    - **JVM** 메모리에 상주(힙 또는 스택)되어 있는 객체 데이터를 연속적인 데이터, **바이트 스트림**(stream of bytes) 형태로 **변환**
- 조건:
    - 직렬화하고 싶은 클래스에 `java.io.Serializable` 인터페이스만 구현하면 된다.
- 사용처:
    - 서블릿 세션 (Servlet Session):
        - 단순히 세션을 서블릿 메모리 위에서 운용한다면 직렬화를 필요로 하지 않지만, 만일 **세션 데이터를 저장 및 공유**가 필요할때 직렬화를 이용한다.
            - 세션 데이터를 DB에 저장할 때
            - 톰캣의 세션 클러스터링을 통해 각 서버간에 데이터 공유가 필요할때
    - 캐시 (Cache):
        - DB부터 조회한 객체 데이터를 다른 모듈에서도 필요할때 다시 DB를 조회하는 것이 아니라, **객체를 직렬화**하여 메모리나 외부 파일에 **저장**해 두었다가 **역직렬화**하여 **사용**하는 **캐시 데이터**로서 이용이 가능하다.
        - 물론 자바 직렬화를 이용해서만 캐시를 저장할 수 있는 것은 아니지만 자바 시스템에서 만큼은 구현이 가장 간편하기 때문에 많이 사용된다고 보면 된다.
            - 요즘 많이 사용되는 Redis, Memcached 와 같은 캐시 DB도 그렇다.
                
                ```java
                @Configuration
                @EnableRedisRepositories
                public class RedisConfig {
                @Bean
                    public RedisTemplate<String, String> redisTemplate() {
                        RedisTemplate<String, String> redisTemplate = new RedisTemplate<>();
                        redisTemplate.setKeySerializer(new StringRedisSerializer()); // 저장 시 key를 String으로 직렬화
                        redisTemplate.setValueSerializer(new StringRedisSerializer()); // 저장 시 value를 String으로 직렬화
                        redisTemplate.setConnectionFactory(redisConnectionFactory());
                        return redisTemplate;
                    }
                ..
                ```
                
                - setKeySerializer, setValueSerializer 설정해주는 이유는 Redis에 저장된 binary형태의 데이터를 String으로 변환시켜서 눈으로 확인하기 위함이다. (redis-cli를 통해서 binary 형태의 데이터를 직접 확인할 수 있다.)
    - 자바 RMI (Remote Method Invocation):
        - 자바 RMI는 원격 시스템 간의 메시지 교환을 위해서 사용하는 자바에서 지원하는 기술이다.
        - 이 메세지에 객체 데이터를 직렬화하여 송신하는 것이다.
        - 최근에는 소켓을 이용하기 때문에 안쓰이는 기술이다.
        

> 💡**바이트 스트림 이란?**
> 
> - 스트림은 클라이언트나 서버 간에 출발지 목적지로 입출력하기 위한 데이터가 흐르는 통로를 말한다.
> - 자바는 스트림의 기본 단위를 바이트로 두고 있기 때문에, 네트워크, 데이터베이스로 전송하기 위해 최소 단위인 바이트 스트림으로 변환하여 처리한다.

### 2. **Serializable 인터페이스**

- 자바 직렬화에 사용되는 Serializable 인터페이스는 메서드가 하나도 없다.
- 이 말은 즉슨 우리가 구현해야할 메소드가 없다는 뜻이다.

```java
package java.io;
//중략
public interface Serializable {
}
```

### 3. **Serializable 인터페이스를 통해 Serialization 수행하기**

- 직렬화 방법:
    - `java.io.ObjectOutputStream`을 이용하여 stream 형태로 만들어 file 에 쓰거나 전송할 수 있다.
    - 자바에서 Jackson 라이브러리를 사용 중에 JSON 데이터의 특정 필드를 제외하려면 @JsonIgnore 어노테이션을 사용하는 것처럼, 자바 직렬화에서는 `transient` 키워드나 `static` 키워드를 사용하여 필드를 제외할 수 있다.
    - 이렇게 마크 되지 않은 필드들은 모두 직렬화된다.
    - 직렬화는 기본적으로 직렬화 대상 클래스의 모든 요소에 대해 자동으로 적용되지만, `writeObject()` 나 `readObject()`를 사용하면 커스텀 직렬화&역직렬화가 가능하다.
        - `writeObject()` 직렬화 시에, `readObject()` 는 역직렬화 시에 사용할 수 있다.
    - code
        
        ```java
        public static void main(String[] args){
            Member member = new Memer("Ally", "deliverykim@baemin.com", 36);
          byte[] serializeMember;
          try(ByeArrayOutputStream baos= new ByteArrayOutputStream()){
            try(ObjectOutputStream oos= new ObjectOutputStream(baos)){
              oos.writeObject(member);
              // serializedMember -> 직렬화된 member 객체
              serializedMember = baos.toByteArray();
            }
          }
          //바이트 배열로 생성된 직렬화 데이터를 base64로 변환
          System.out.println(Base64.getEncoder().encodeToString(serializedMember));
        }
        ```
        

- 객체 상속 관계에서의 직렬화:
    - 부모 클래스가 Serializable을 구현했다면 자식 클래스는 Serializable을 구현하지 않아도 직렬화가 가능하다.
    - 하지만, 반대로 부모 클래스는 Serializable을 구현하지 않고 자식 클래스만 구현했다면 부모 필드는 무시하고, 자식 필드만 직렬화가 된다.
        - 따라서, **자바 직렬화는 보통 부모 클래스가 serializable 이 아닌데, 자식 클래스가 serializable 한 경우 부모의 필드들도 serialize 시켜줄 때 사용되거나, 어떤 값이 time base 와 같이 시점에 의미를 갖는 값일 때 등의 경우에 사용된다.**
- 발생할 수 있는 예외:
    - InvalidClassException
        - 1) 클래스의 SerialVersionUID 버전이 다른 경우, 2) 클래스에 다른 데이터 타입을 포함한 경우, 3)기본 생성자가 없는 경우에 예외가 발생한다.
            - 클래스의 SerialVersionUID 버전이 다른 경우
                - 네트워크로 객체를 직렬화하여 전송하거나 협업을 하는 경우 수신자와 송신자 모두 같은 버전의 클래스를 가지고 있어야 할텐데, 만일 클래스가 조금만 변경사항이 있으면 모든 사용자에게 재배포해야 하는 애로사항이 생겨 프로그램을 관리하기 어렵게 만든다.
                - 따라서 직렬화 클래스는 왠만한 상황에선 serialVersionUID 를 직접 명시해주어 **클래스 버전을 수동으로 관리**하는 것을 권장하는 편이다.
                - 이 때 UID 버전이 다르면
            - 필드 타입이 변경된 경우
                - 예를들어 Member 클래스의 age를 int 형 에서 long 형으로 업데이트하고 역직렬화를 해보면 incompatible type 에러가 발생한다.
            - 기본 생성자가 없는 경우
                - 상속 관계에서 직렬화를 수행했을 때 발생한다.
                - 부모 클래스가 non-serializable 한다면 역직렬화하는 과정에서 직렬화되지 않은 부모의 속성 정보들을 기본 생성자를 통해서 가져오게 되는데, 이 때 부모에 기본 생성자가 없으면 예외가 발생한다.
    - NotSerializableException
        - 부모 자식간의 관계가 아닌 단순 참조하는 클래스 객체 멤버 변수가 non-serializable 하다면 예외가 발생하면서 직렬화에 실패한다.

### 4. **Serializable** 단점

- 유지보수의 어려움
    - 클래스가 Serializable을 구현하게 되면, 릴리즈 후에 직렬화된 바이트 스트림 인코딩도 하나의 공개 API가 되기 때문에 직렬화을 구현한 클래스가 널리 퍼지면 그 직렬화 형태도 영원히 지원해야한다.
    - 즉, Serializable을 구현한 순간부터 해당 객체의 유지보수는 직렬화에 묶이게 되는 것이다.

### 5. 결론

- 자바 직렬화는 이진 형태로 변환하여 저장하거나 네트워크를 통해 전송하여 협업하는 데에 쓰일 수 있지만, 보안 및 버전 관리 측면에서 문제가 될 수 있으며, 클래스 구조 변경 시 호환성 문제가 발생할 수 있다.
- **자주 변경되는 비즈니스적인 데이터는 자바 직렬화를 사용하지 않는 것**이 권장되며**,** 외부 저장소로 저장되는 데이터(ex. Redis)는 짧은 만료시간의 데이터를 제외하고 자바 직렬화를 사용을 지양하는 것이 좋다.
    - 긴 만료 시간을 가지는 데이터는 JSON 등 다른 포맷을 사용하여 저장하는 것이 권장된다.
    - 자바에서 Object를 XML, JSON과 같은 포맷을 이용한 직렬화는 다른 환경, 다른 언어로 만들어진 어플리케이션과도 통신이 가능하다.

### References

- [자바 직렬화(Serializable) - 완벽 마스터하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A7%81%EB%A0%AC%ED%99%94Serializable-%EC%99%84%EB%B2%BD-%EB%A7%88%EC%8A%A4%ED%84%B0%ED%95%98%EA%B8%B0)
- [[Java] writeObject 와 readObject 는 무엇인가?](https://aroundck.tistory.com/3114)
- [Java의 직렬화(Serialize)란? - 고코딩 - 티스토리](https://go-coding.tistory.com/101)
- [Serializable 인터페이스에 대하여](https://1-7171771.tistory.com/116)
- [[Redis] SpringBoot Redis 적용기](https://seungjjun.tistory.com/245)
- [JAVA의 객체 직렬화(Serialization)와 JSON 직렬화](https://atoz-develop.tistory.com/entry/JAVA%EC%9D%98-%EA%B0%9D%EC%B2%B4-%EC%A7%81%EB%A0%AC%ED%99%94Serialization%EC%99%80-JSON-%EC%A7%81%EB%A0%AC%ED%99%94)
