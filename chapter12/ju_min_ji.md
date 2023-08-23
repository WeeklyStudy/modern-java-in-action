## Q1. **ZonedDateTime과 OffsetDateTime는 어떤 차이점이 있고 각각 어느 상황에 사용하면 좋을까?**

### 1. ZoneId vs ZoneOffset

- `ZoneId`은 타임존(시간대), `ZoneOffset`은 시차를 나타낸다.
- `ZoneOffset`은 **UTC** 기준으로 고정된 시간 차이를 양수나 음수로 나타내는 반면, `ZoneId`는 이 시간 차이를 타임존 코드로 나타낸다.
    - 예를 들어 서울의 경우 타임존 코드는 `Asia/Seoul`이고 시차는 `+09:00` 이다.
    - 시차가 `+0:00`이면, `OffsetDateTime`은 UTC를 기준으로 하는 `Instant`와 같은 moment를 의미한다.
    
 

#### 💡 *타임존(Time-Zone)*

> - Time-Zone은 여러 가지 다른 것을 설명하는 데 사용할 수 있지만 대개 지역 또는 국가의 현지 시간을 나타내며, 주로 해당 국가에 의해 법적으로 지정된다.
> - 타임존은 `ZoneId` 클래스를 통해 날짜/시간별 DST가 반영되었는지 확인할 수도 있다. `ZoneId.getAvailableZoneIds()` 를 통해 지원하는 지역별 타임존을 확인할 수 있다(현재 시점 등록된 ZoneId는 600개이다).

#### 💡 *DST(Daylight Saving time)*

> - DST은 자연 일광을 보다 잘 활용하기 위해서 여름철에 표준 시간에서 1시간 앞으로, 그리고 다시 가을에 시간을 1시간 전으로 설정하는 것을 말한다.
> - DST와 "summer time"은 같은 말을 뜻하며 특정 나라에서 주로 불린다.
> - 영국에서 썸머타임이라고 많이 사용하며, DST가 적용되지 않는 표준시는 "winter time"이라고 사용되기도 한다.

### 2. ZonedDateTime vs OffsetDateTime

- `ZonedDateTime` , `OffsetDateTime`는 공통적으로 나노초 단위의 타임라인 상의 인스턴트를 저장한다.
- 하지만, `OffsetDateTime`은 `LocalDateTime`+`ZoneOffset`이 결합된 클래스이고, `ZonedDateTime` 은 `LocalDateTime` + `ZoneId`가 결합된 클래스이다.
- 즉, `OffsetDateTime`과 달리, `ZonedDateTime` 은 일광 절약 시간(DST) 조정 및 기타 이상 현상을 다루는 규칙을 포함한다.
    - 타임 존 = ( Offset-From-UTC + 이상 현상 규칙 )
    - DST에는 CET(겨울), CEST(여름) 형태가 있는데 Time Zone(CET)로 통일하고, Time Transition Rule을 가지는 `ZoneRules`를 통해 알아서 내부적으로 계산해준다.
- 따라서, DST 등으로 인해 **고정된 시차**를 가지지 않는 도시를 대상으로 날짜 시간 객체를 만들 때는 `OffsetDateTime`보다는 `ZonedDateTime` 을 사용하는 편이 적합하다.
    - 계절에 따라 변하는 시차를 알아서 처리해주기 때문입니다.
    - 예를 들어 벤쿠버의 경우 보통은 시차가 `-08:00`이지만 소위 Summer Time이라고 불리는 일괄절약타임을 시행하기 때문에 여름에는 한 시간 더 일찍 시간이 간다.
- 하지만, [공식 API 문서](https://docs.oracle.com/javase/8/docs/api/java/time/OffsetDateTime.html)에 따르면, **데이터베이스**나 **네트워크**와 **통신**할 때는 `OffsetDateTime` 사용이 권장된다.
   - local time offsets을 포함한 `OffsetDateTime`는 언제나 동일한 시간 순간을 나타내지만, 시간대(zone-id) 변경이나 규칙 조정과 관련된 정보가 없어서 안정된 순서를 가진다.
   - 반면, full timezone 정보를 포함한 `ZonedDateTime`는 해당 시간대의 규칙이 조정되더라도 해당 변화를 반영할 수 있어서 불안정한 의미를 가질 수 있기 때문이다.
   - 예를 들어, `ZonedDateTime`을 저장하고 검색할 경우, 저장된 computed offset과 검색된 객체의 offset이 zone-id의 현재 규칙과 일치하지 않는 문제가 발생할 수 있다.
### 3. 결론
- **사용자와 상호작용하거나 메시지를 표시할 때**, 그리고 **사용자의 시간대 정보를 고려해야 할 때**는 `ZonedDateTime을` 사용한다.
  - 이는 특정 시간을 특정 지역의 시간대로 표현하고 다룰 수 있게 해주기 때문이다.
- 반면, **시간 이벤트를 데이터베이스에 저장**하거나 **시간의 절대적인 순서를 중요하게 다룰 때**는 `OffsetDateTime`을 사용한다.
  - 이는 시간 정보를 특정 지역 시간대로 변환하지 않고 영향을 받지 않는 순수한 시간 차이를 표현하기 때문이다.


### References

- [날짜와 시간 API with Java8 - TimeZone, DST](https://wickso.me/java/java-8-date-time.html#_localdate)
- [[Java8 Time API] ZonedDateTime 사용법](https://www.daleseo.com/java8-zoned-date-time/)
- [Java 8 ZonedDateTime vs OffsetDateTime 어떤 상황에서 쓰는게 적합한가?](https://sujl95.tistory.com/86)
- [ZonedDateTime과 OffsetDateTime의 차이점](https://howtodoinjava.com/java/date-time/zoneddatetime-vs-offsetdatetime/)
- [[StackOverFlow] What's the difference between ZonedDateTime and OffsetDateTime? *](https://stackoverflow.com/questions/30234594/whats-the-difference-between-zoneddatetime-and-offsetdatetime)
- [Java8+ Instant vs LocalDateTime 각 사용방법](https://velog.io/@lsb156/Instant-vs-LocalDateTime)

## Q2. **Java 8의 Instant vs LocalDateTime**

### 1. Instant 클래스

- 도입 배경
    - Instant 클래스가 도입되기 전에 많은 사람들은 long형 Unix Timestamp(POSIX Time, Epoch Time)를 사용했다.
    - 이는 integer, long type을 이용한 정렬 / 연산등에서 다른 타입들보다 빠르기 때문이다.
    - 하지만 Unix Timestamp는 [2038년 문제](https://ko.wikipedia.org/wiki/2038%EB%85%84_%EB%AC%B8%EC%A0%9C)가 있기 때문에, 이를 해결할 다른 타입이 필요했다.
- 특징
    - **UTC**로 표현한 타임라인의 특정 순간(moment)에 대한 정보로, 1970년 1월 1일 UTC의 첫 번째 순간 이후의 현재 시간까지의 **나노초**를 나타낸 값이다.
    - Instant는 항상 UTC(+00:00)을 기준으로 하기 때문에, LocalTime과는 차이가 있을 수 있다. 우리나라의 경우에는 시간대가 '+09:00'이므로 Instant와 LocalTime간에는 9시간의 차이가 있다.
    - `long` 형태로 `Unix Timestamp`를 저장하기 때문에 연산이 빠르다.

#### 💡 UTC (Coordinated Universal Time)

> 세슘 원자시계와 태양시의 차이의 오차를 보정하는데 이 1초를 윤초(*leap second*)라고 한다. UTC는 영국 그리니치 천문대를 기준으로 윤초를 보정하여 1970년 1월 1일 0시 0분 0초를 시작으로 정의한다. 이러한 협정 세계시는 1972년 1월 1일부터 국제적인 표준시가 되었다.
> 
> 
> UTC는 국제 표준시를 뜻하며 타임존은 아니다. 즉, 공식적으로 UTC를 현지 시간으로 사용하는 국가나 지역은 없다. 
> 
> 사실 영국에서는 GMT(Greenwich Mean Time) 라고 이름을 지었으나, 세계의 반발으로 UTC로 이름이 정해진다.
> 

### 2. **LocalDateTime 클래스**

- 특징
    - Java Time에서 `Local`이 들어간다는것은 시간대(Zone Offset/Zone Region)에 대한 정보가 없다는 의미다.
    - 따라서, LocalDateTime은 로컬 pc의 날짜와 시간을 반환한다.
    
    ```java
    // 로컬 시간을 의미하는 ISO 8601 문자열
    2017-11-06T15:00:00.000
    
    // UTC(GMT) 시간을 의미하는 ISO 8601 문자열
    2017-11-06T06:00:00.000Z
    
    // 로컬 시간을 의미하면서 UTC(GMT) 대비 +09:00 임을 의미하는 ISO 8601 문자열
    2017-11-06T15:00:00.000+09:00
    ```
    

### 3. Instant vs LocalDateTime

- 정밀도
    - LocalDateTime에서 밀리초, 나노초는 옵션이지만, Instant는 기본 값이 밀리초이다.
    - 따라서, Instant는 LocalDateTime보다 정확하고 정밀한 타임스탬프를 제공한다.
- 시간대 고려 여부
    - 글로벌 런칭한 서비스 비즈니스 앱 개발 시 Instant 나 ZonedDateTime 클래스 (`ZoneId`+ `Instant` )를 많이 사용한다.
- 순간적인 의미
    - LocalDateTime에는 의도적으로 시간대 또는 UTC와의 오프셋 개념이 없다.
    - 따라서 로깅과 같이 순간을 나타낼 때에는 `Instant.now()` 를 사용하는 것이 적합하다.
- 인간 친화적
    - LocalDateTime은 인간 친화적이기 때문에 FrontEnd Service, 즉 Display(View)에 좋다.

### 4. 결론

- Instant 클래스는 기계친화적이기 때문에, 데이터 교환, 직렬화 시나리오 등에 적합하다. 빠른 속도로 연산 가능하기 때문이다.
- [JDBC 4.2](http://openjdk.java.net/jeps/170) 이상 과 호환되는 [JDBC 드라이버](https://en.wikipedia.org/wiki/JDBC_driver) 사용 시  *java.time* 객체를 데이터베이스와 직접 교환할 수 있다. Hibernate 5 및 JPA 2.2는 *java.time을* 지원하기 때문이다.
- 따라서, LocalDateTime은 인간 친화적이긴 하지만, 국제적으로 공통된 기준이 필요할 때(나이, 생일 등)는  입력 값을 그대로 DB에 저장하지 말고 Instant 등으로 저장 후 서버에서 계산/변환해서 사용하는 것이 권장된다.

### References

- [Java 8 LocalDateTime.now() only giving precision of milliseconds](https://stackoverflow.com/questions/39586311/java-8-localdatetime-now-only-giving-precision-of-milliseconds)
- [Java 에서 시간을 관리하는 방법 (Instant.now(), LocalDate.now())](https://jiwondev.tistory.com/286)
- [Java 8 LocalDateTime vs Instant 어떤 상황에서 쓰는게 적합한가?](https://sujl95.tistory.com/85)
- [[자바의 정석] Instant, LocalDateTime과 ZonedDateTime 차이](https://serina-the-best.tistory.com/35)
- [날짜와 시간 API with Java8 - UTC](https://wickso.me/java/java-8-date-time.html#_instant)
- [컴퓨터 시간의 1970년은 무슨 의미일까?](https://futurecreator.github.io/2018/06/07/computer-system-time/)

## Q3. **java.time 패키지 내부는 어떻게 되어 있을까?**

### 1. java.time 패키지

- Java의 탄생과 함께한 Date와 Calendar의 단점을 해소하기 위해 JDK 1.8부터 추가된 패키지이다.
- 특징 : 불변(immutable)
    - 날짜나 시간을 변경하는 메서드들 : 기존의 객체 자체를 변경하는 대신 항상 변경된 새로운 객체를 반환한다.
    - 가변 클래스였던 기존의 Date, Calendar클래스와 달리 멀티 쓰레드 환경에서도 Thread-Safe하다.
- 하위 패키지 4가지
    
    
    | java.time.chrono | 표준(ISO)이 아닌 달력 시스템을 위한 클래스들을 제공 |
    | --- | --- |
    | java.time.temporal | 날짜와 시간의 필드(field)와 단위(unit)을 위한 클래스들을 제공 |
    | java.time.format | 날짜와 시간을 파싱하고, 형식화하기 위한 클래스들을 제공 |
    | java.time.zone | 시간대(time-zone)와 관련된 클래스들을 제공 |
- 날짜와 시간, 시간대를 하나로 표현한 Calendar와 달리 날짜와 시간을 별도의 클래스로 구분한다.
    - java.time 패키지 내부 핵심 클래스
        
        
        | LocalDate | 날짜 |
        | --- | --- |
        | LocalTime | 시간 |
        | LocalDateTime | 날짜 & 시간 |
        | ZonedDateTime | 날짜 & 시간 & 시간대(time-zone) |

### 2. java.time 하위 패키지 인터페이스 - Temporal, TemporalAdjuster 인터페이스

- Temporal 인터페이스
    
    ![image](https://github.com/WeeklyStudy/modern-java-in-action/assets/63441091/bc8887b0-098b-473b-9341-12f8f98bd91a)

    
    - TemporalAccessor를 상속하는 Temporal 인터페이스는 ****특정 시간을 모델링하는 객체의 값을 어떻게 읽고 조작할지 정의한다.
        
        ```java
        public interface Temporal extends TemporalAccessor {
        
        boolean isSupported(TemporalUnit unit);
        
        default Temporal with(TemporalAdjuster adjuster) {
                return adjuster.adjustInto(this);
            }
        
        Temporal with(TemporalField field, long newValue);
        
        default Temporal plus(TemporalAmount amount) {
                return amount.addTo(this);
            }
        
        Temporal plus(long amountToAdd, TemporalUnit unit);
        
        default Temporal minus(TemporalAmount amount) {
                return amount.subtractFrom(this);
            }
        
        default Temporal minus(long amountToSubtract, TemporalUnit unit) {
                return (amountToSubtract == Long.MIN_VALUE ? plus(Long.MAX_VALUE, unit).plus(1, unit) : plus(-amountToSubtract, unit));
            }
        
        long until(Temporal endExclusive, TemporalUnit unit);
        }
        ```
        
- TemporalAdjuster 인터페이스
    - 자주 쓰일만한 날짜 계산들을 대신 해주는 메서드를 정의한다.
    - 복사본을 생성하기 위해 **Temporal 개체**를 ***조정(adjust)하는*** 도구이다.
    - 기본적으로 TemporalAdjuster는 Temporal 개체를 직접 조정하는 것이 아니라 조정하는 프로세스를 외부화하기 위해 존재한다.
        
        ```java
        @FunctionalInterface
        public interface TemporalAdjuster {
            Temporal adjustInto(Temporal temporal);
        }
        ```
        
    - 추상메서드 하나만 정의되어있는 함수형 인터페이스로, 이 메서드만 구현하면 된다.
- 구현 클래스 :
    - LocalDate, LocalTime, LocalDateTime, ZonedDateTime, Instant 등
        
        ```java
        @jdk.internal.ValueBased
        public final class LocalDate
                implements Temporal, TemporalAdjuster, ChronoLocalDate, Serializable {
        ```
        
        ```java
        @jdk.internal.ValueBased
        public final class LocalTime
                implements Temporal, TemporalAdjuster, Comparable<LocalTime>, Serializable {
        ```
        
        ```java
        @jdk.internal.ValueBased
        public final class LocalDateTime
                implements Temporal, TemporalAdjuster, ChronoLocalDateTime<LocalDate>, Serializable {
        ```
        
        ```java
        @jdk.internal.ValueBased
        public final class ZonedDateTime
                implements Temporal, ChronoZonedDateTime<LocalDate>, Serializable {
        ```
        
        ```java
        @jdk.internal.ValueBased
        public final class Instant
                implements Temporal, TemporalAdjuster, Comparable<Instant>, Serializable {
        ```
        

### 3.  java.time 하위 패키지 인터페이스 - ChronoPeriod 인터페이스

- ChronoPeriod 인터페이스
    - TemporalAmount 인터페이스를 상속한 ChronoPeriod 인터페이스는 두 날짜 사이의 시간의 양을 advanced globalization 사용 사례를 위해 정의한다.
        
        ```java
        public interface ChronoPeriod
                extends TemporalAmount {
        ```
        
        ```java
        public interface TemporalAmount {
        
            long get(TemporalUnit unit);
        
            List<TemporalUnit> getUnits();
        
            Temporal addTo(Temporal temporal);
        
            Temporal subtractFrom(Temporal temporal);
        }
        ```
        
- 구현 클래스 :
    - Duration, Period

### 4.   java.time 하위 패키지 인터페이스 - TemporalUnit 인터페이스

- TemporalUnit 인터페이스
    - 날짜와 시간 단위(예: 초 또는 일)를 정의한다.
- 구현 클래스 :
    - ChronoUnit
        - [열거형 상수 및 메서드](https://docs.oracle.com/javase/8/docs/api/java/time/temporal/ChronoUnit.html)

### 5. java.time 하위 패키지 인터페이스 - TemporalField 인터페이스

- TemporalField 인터페이스
    - 월(month-of-year)과 시(hour-of-day)와 같이 날짜와 시간과 관련된 필드를 정의한다.
    - 날짜와 시간 객체에 접근하여 특정 필드의 값을 변경하기 위해서 with() 메소드를 사용한다.
        
        ```java
        // LocalDate 객체의 특정 필드 값 변경하기
        LocalDate with(TemporalField field, long newValue)
        
        // LocalDate 객체의 특정 연도 필드 값 변경하기
        LocalDate withYear(int year)
        ```
        
- 구현 클래스 :
    - ChronoField
        - [열거형 상수 및 메서드](https://docs.oracle.com/javase/8/docs/api/java/time/temporal/ChronoField.html)

### References

- [LocalDate와 LocalTime](http://www.tcpschool.com/java/java_time_localDateTime)
- [[Java] java.time패키지(1) *](https://gptjs409.github.io/java/2019/09/14/time-1.html)
- [Java TemporalAccessor Tutorial with Examples](https://o7planning.org/13761/java-temporalaccessor)
