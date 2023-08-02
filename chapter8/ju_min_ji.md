## Q1. **팩토리 메서드란 무엇인가?**

### 1. 팩토리 패턴이란?

- 정의 :
    - 객체 생성 역할을 별도의 클래스(Factory)에 위임하는 패턴이다.
- 궁극적인 목표 :
    - 객체는 여러 곳에서 생성될 수 있는데, 호출하는 쪽이 객체의 생성자에 직접 의존하고 있으면 나중에 변경되었을 때 수정되어야 하는 코드가 많이 발생한다.
    - 따라서, **클라이언트 코드와 결합도(의존성)를 낮추고**, **코드를 건드리는 횟수를 최소화**하기 위해 객체 생성 코드를 분리하는 것이다.
- 적용되는 객체지향 프로그래밍(OOP) 원칙 :
    - 추상화 :
        - 팩토리 패턴은 클라이언트 코드에 구체적인 객체 생성 코드를 노출시키지 않고, 추상화된 인터페이스나 추상 클래스를 통해 객체를 생성
    - 캡슐화(Encapsulation) :
        - 객체 생성과 관련된 복잡한 로직이나 조건을 한 곳에 집중하여 유지보수를 용이하게 함
    - 의존성 역전 원칙(DIP, Dependency Inversion Principle) :
        - 클라이언트 코드는 인터페이스나 추상 클래스와 같이 추상화된 형태에 의존하게 되어, 구체적인 클래스에 대한 의존성을 줄임
    - 개방/폐쇄 원칙 (OCP, Open/Closed Principle)
        - 팩토리 메서드 패턴과 추상 팩토리 패턴은 기존 클래스에 영향 주지 않고 확장 가능
- 종류 :
    - **심플 팩토리 패턴** :
        - 생성자 호출(`new`)을 별도의 클래스(Factory)에서 담당하고 클라이언트 코드에서는 Factory를 통해 객체를 생성
        - 한계 :
            - 하지만 새로운 클래스가 추가되었을 때 Factory 클래스를 수정해야 한다는 한계가 있다.
            - 따라서 이러한 문제를 팩토리 메서드 패턴이나 추상 팩토리 패턴으로 해결 가능하다.
    - **팩토리 메서드 패턴** :
        - 객체 생성을 위한 인터페이스를 정의하고, **어떤 클래스의 인스턴스를 생성**(`new`)할지에 대한 처리는 **서브클래스가 결정**
    - **추상 팩토리 패턴** :
        - 팩토리 메서드 패턴보다 더 추상화된 패턴으로, 인스턴스 생성을 위해 연관된 **서브 클래스를 그룹화**할 수 있고 이것은 **이 그룹을 자유롭게 교체**할 수 있는 패턴
        

### 2. 팩토리 메서드 패턴 장단점 및 사용 사례

- 장점 :
    - 확장성
        - **새로운 구현 클래스**가 만약 추가되어도 **기존 코드 수정 없이** 새로운 팩토리를 만들어 내기만 하면 된다.
    - 가독성
        - 객체를 new 키워드를 통해 생성자로 생성하려면 개발자는 해당 생성자의 인자 순서와 내부 구조를 알고 있어야 목적에 맞게 객체를 생성할수가 있다는 번거로움이 있다.
        - 하지만, **반환될 객체의 특성**을 적절하게 표현하는 **메서드 네이밍**으로 반환될 객체의 특성을 유추하도록 도울 수 있다.
    - 캡슐화
        - 실제 구현 내용을 외부에 감추고, **인스턴스에 대해 통제 및 관리**가 가능하다.
            - 일반적인 객체 생성은 생성자를 통해 객체를 생성하게 되는데, 이 경우 객체 생성에 대한 통제나 관리가 제한적이다.
            - 그러나, 팩토리 메서드를 통해 생성된 객체는 필요에 따라 항상 새로운 객체를 생성하여 반환할 수 있고, 기존에 생성한 객체를 캐싱하여 재사용하게 할 수 있으며, 객체 생성에 필요한 초기화 작업을 메서드 내에서 처리할 수 있다.
    - 다형성
        - **인자에 따라 다른 객체를 반환**하도록 분기할 수 있다.
            
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
            
- 단점 :
    - 복잡성
        - 객체 생성 로직이 여러 개의 서브클래스에 분산되어 있을 수 있어서, 팩토리 메서드 패턴을 사용하면 클래스 계층 구조가 복잡해질 수 있다. 이로 인해 유지보수가 어려울 수 있다.
    
- 사용 사례
    1. 복잡한 객체 생성(Complex object creation)
    2. 객체 커스텀(Object customization)
    3. 오브젝트 풀링(Object Pooling)

- 프로젝트 예제 코드
    - 원하는 RestTemplate 객체 커스텀하기 위해 팩토리 메서드 패턴 사용하기
        - `RestTemplateConfig` 클래스는 `RestTemplate` 객체를 생성하는 팩토리 메서드 패턴 사용
        - `restTemplate()` 메서드는 `RestTemplate` 객체를 생성하고 필요한 설정 적용 후 반환
        
        ```java
        import com.idea5.four_cut_photos_map.global.error.RestTemplateResponseErrorHandler;
        import org.apache.http.client.HttpClient;
        import org.apache.http.impl.client.HttpClientBuilder;
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        import org.springframework.http.client.HttpComponentsClientHttpRequestFactory;
        import org.springframework.web.client.RestTemplate;
        
        @Configuration
        public class RestTemplateConfig {
            private final int READ_TIMEOUT = 5000;      // 읽기시간초과(ms)
            private final int CONNECT_TIMEOUT = 5000;   // 연결시간초과(ms)
            private final int MAX_CONN_TOTAL = 100;     // 최대 오픈되는 커넥션 수
            private final int MAX_CONN_PER_ROUTE = 5;   // IP, 포트 1쌍에 수행할 커넥션 수
        
            @Bean
            public RestTemplate restTemplate() {
                HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory();
                factory.setReadTimeout(READ_TIMEOUT);
                factory.setConnectTimeout(CONNECT_TIMEOUT);
        
                HttpClient httpClient = HttpClientBuilder.create()
                        .setMaxConnTotal(MAX_CONN_TOTAL)
                        .setMaxConnPerRoute(MAX_CONN_PER_ROUTE)
                        .build();
        
                factory.setHttpClient(httpClient);
        
                RestTemplate restTemplate = new RestTemplate(factory);
                // 에러 핸들러 설정 추가
                restTemplate.setErrorHandler(new RestTemplateResponseErrorHandler());
                return restTemplate;
            }
        }
        ```
        

### References

- [All about Java — Part1(a) *](https://medium.com/@adititripathi694/all-about-java-part1-a-38283e76b799)
- [https://inpa.tistory.com/entry/GOF-💠-정적-팩토리-메서드-생성자-대신-사용하자](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%83%9D%EC%84%B1%EC%9E%90-%EB%8C%80%EC%8B%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EC%9E%90)
- https://www.crocus.co.kr/1543
- https://fvor001.tistory.com/63
- https://bcp0109.tistory.com/368
- https://refactoring.guru/ko/design-patterns/factory-method

## Q2. **Arrays.asList() vs List.of()**

- **리스트 변경 가능 여부**
    - `Arrays.asList()` **:**
        - 원소 추가/삭제 불가, **set()을 통한 변경 가능**
            - Arrays 클래스 내부에 구현된 또다른 별개의 ArrayList 객체 반환하기 때문이다.
    - `List.of()` :
        - 원소 추가/삭제 불가, **set()을 통한 변경 불가**
            - ImmutableCollections 객체의 내부 클래스인 ListN 객체 반환하기 때문이다.
- **리스트 내부 배열 참조 여부**
    - `Arrays.asList()` **:**
        - 참조한 원본 배열의 값이 바뀌면 리스트의 값도 바뀌고, 반대로 List의 값을 수정해도 원본 배열의 값도 바뀌게 된다.
    - `List.of()` :
        - 참조한 원본 배열의 값이 바뀌어도 리스트의 값은 바뀌지 않는다.
- ****NULL 값을 가질수 있는 여부****
    - `Arrays.asList()` **:**
        - Null 값 가질 수 있다.
    - `List.of()` :
        - Null 값 가질 수 없다.
- ****메모리 사용량 차이****
    - 일반적으로 변경 불가능한 컬렉션 인스턴스는 JVM 내에서 캐싱될 수 있기 때문에 변경 가능한 컬렉션 인스턴스보다 훨씬 적은 메모리를 사용한다.
    - 메모리 측면에서 권장되는 메서드 : `Arrays.asList()` < `List.of()`

### Reference

- [https://inpa.tistory.com/entry/JAVA-☕-ArraysasList-와-Listof-차이-한방-정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-ArraysasList-%EC%99%80-Listof-%EC%B0%A8%EC%9D%B4-%ED%95%9C%EB%B0%A9-%EC%A0%95%EB%A6%AC)

## Q3. **Iterator, Entry 의 특징과 주요 메서드는 어떤것이 있을까?**

- Iterator
    - 정의
        - Iterator는 자바의 Collections Framework에서 컬렉션에 저장되어 있는 요소들을 읽어오는 방법을 표준화한 것
    - 특징
        - 순차적 접근
        - 변경 감지
            - 변경이 감지되었을 경우 ConcurrentModificationException 발생시킴
        - 순회 종료 후 재사용 불가능
- 주요 메서드
    
    
    | hasNext() | 읽어올 요소가 있으면 true, 없으면 false 리턴 |
    | --- | --- |
    | next() | 다음 데이터를 리턴 |
    | remove() | next()로 리턴받은 요소를 삭제 |
- Map.Entry
    - 정의
        - Map 인터페이스에서 사용되는 중첩 인터페이스로서, Map의 요소인 Key-Value 쌍을 표현하는 데 사용
    - 특징
        - Key-Value 쌍을 표현
        - Value 변경 가능
    - 주요 메서드
        
        
        | boolean equals(Object o) | 동일한 Entry 인지 비교한다. |
        | --- | --- |
        | Object getKey() | Entry의 key 객체를 반환한다. |
        | Object getValue() | Entry 의 value 객체를 반환한다. |
        | int hashCode() | Entry 의 해시코드를 반환한다. |
        | Object setValue(Object value) | Entry의 value 객체를 지정된 객체로 변경한다. |

### Reference

- https://lasbe.tistory.com/63
- https://junecode.tistory.com/113
