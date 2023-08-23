# https://github.com/WeeklyStudy/modern-java-in-action/issues/29 Java 8의 Instant vs LocalDateTime

## 💡LocalDateTime

> 로컬(PC)의 날짜, 시간을 표현하는 클래스(날짜 + 시간)
> 

![Untitled (10)](https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/9307dac4-2c09-4beb-bf73-65836d4860b7)

### 1. 특징
- `타임존(시간대)`를 나타내지 않는다. 즉, 한 지역 혹은 시간대에 묶이지 않는다.
- `시차(ZoneOffset)` 혹은 `타임존(ZoneId)` 같은 추가 정보가 제공되지 않는 한 타임라인의 특정 지점을 나타낼 수 없다.
- 대략적인 날짜 혹은 시간을 나타내기 때문에 실제 런칭 서비스에서는 자주 사용되지 않는다.(보통 `Instant` 혹은 `ZonedDateTime` 를 사용함)

### 2. LocalDateTime 을 사용했을 때 문제 상황

한국-서울(Asia/Seoul), 프랑스-파리(Europe/Paris)에서 글로벌 서비스를 운영하고 있다고 가정한다.

각 리전에 위치한 서버의 타임존은 다를 것이다.

- 한국-서울 : UTC +9
- 프랑스-파리 : UTC +2

각 인스턴스에서 `LocalDateTime.now()` 로 DB에 날짜/시간 정보를 저장하면 타임존을 UTC로 설정해주지 않는 이상, 각 타임존에 맞게 데이터가 저장된다.
- 한국-서울 : 2023-08-21 19:00:00
- 프랑스-파리 : 2023-08-21 12:00:00

한국 타임존에서 `2023-08-21 19:00:00`와 프랑스 타임존에서 `2023-08-21 12:00:00`은 동일한 시점이지만 DB에는 타임존 정보가 저장되지 않기 때문에 문제가 생긴다.

### 3. LocalDateTime을 사용하는 경우

1. 특정 날짜/시간을 여러 리전에서 적용해야하는 경우
    - 글로벌 서비스에서 2024-01-01 00:00:00에 새해 알림을 해야하는 경우
    - 서울과 파리는 서로 다른 시간대를 사용하므로 특정 시점(타임존)을 적용하면 안된다.
2. 글로벌 서비스가 아닌 단일 리전 서비스인 경우
    - 한국에서만 운영하는 서비스라면 굳이 타임존 개념을 사용할 필요는 없다.

## 💡Instant

> UTC 타임라인의 특정 시점의 타임 스탬프를 표현하는 클래스(날짜 + 시간 + UTC)
> 

> 📍**타임 스탬프**
> 
> 
> EPOCH TIME(1970-01-01 00:00:00 [UTC](https://ko.wikipedia.org/wiki/%ED%98%91%EC%A0%95_%EC%84%B8%EA%B3%84%EC%8B%9C))로부터 경과된 시간을 나노초(10^-9) 단위의 양수 또는 음수로 표현한 값이다.
> 

![Untitled (11)](https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/f341ee75-1848-48ee-ad5d-5ad1effd8ad8)

### 1. 특징
- 인간보다 기계에 친화적이다.
    - toString()을 재정의하여 사람이 쉽게 읽을 수 있는 형태로 출력되는 것 뿐이다.
- long 형태의 Unix Timestamp를 저장하기 때문에 연산 속도가 빠르다.
- 대부분의 비즈니스 로직, 데이터 저장 및 변경은 UTC 기준이므로 사용하기에 편리하다.

### 2. Instant를 사용하는 경우

1. 타임스탬프를 UTC 기준으로 저장하는 경우
    - DB, 백엔드 비즈니스 로직, 데이터 교환 등
2. 글로벌 서비스를 개발하는 경우

## 💡결론

### 1. Java 8+ 날짜 다루기
![Untitled (12)](https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/d50139d2-8710-4fb5-8fbe-ae6c93df788c)

| 클래스 | 설명 |
| --- | --- |
| LocalDate | 로컬 날짜 |
| LocalTime | 로컬 시간 |
| LocalDateTime(LocalDate + LocalTime) | 로컬 날짜+시간 |
| ZonedDateTime(LocalDateTime + 타임존/시차) | 지정한 시간대의 특정 시점(날짜+시간) |
| Instant(LocalDateTime + UTC) | UTC의 특정 시점(날짜+시간) |
- 한국처럼 전국적으로 타임존이 동일한 곳에서 단일 리전 서비스를 런칭하는 경우엔 `LocalDateTime` 을 사용해도 무방하다.
- 하지만 글로벌 서비스를 런칭하는 경우엔 `Instant` 를 사용하는 것이 좋다.
- UTC 기준으로 DB에 데이터를 저장하고 사용자의 타임존을 적용하여 보여준다.

### 2. 우리 프로젝트에서는?

네컷지도 프로젝트는 서울 리전의 EC2 서버 1대로 운영되고 있다. 글로벌 서비스를 지원할 계획도 없기 때문에 `LocalDateTime` 타입을 써도 괜찮을 것 같다.

자바에서는 `LocalDateTime` 타입을 사용하고 있고 DB(MySQL)에서는 `datetime` 타입을 사용 중인데 여유가 된다면 MySQL의 `datetime` 과 `timestamp` 타입의 차이에 대해서도 공부해보면 좋을 것 같다.

## Reference

- Instant vs LocalDateTime
    - [Java 8 LocalDateTime vs Instant 어떤 상황에서 쓰는게 적합한가?*](https://sujl95.tistory.com/85)
    - [Java 날짜 시간 유형에 대한 고찰*](https://jaimemin.tistory.com/1537)
    - [Java8+ Instant vs LocalDateTime 각 사용방법](https://velog.io/@lsb156/Instant-vs-LocalDateTime)
    - [[JAVA] LocalDateTime vs. Instant (feat. ZonedDateTime, OffsetDateTime)](https://dev-minjeong.tistory.com/31)
- Instant
    - [[Java8 Time API] Instant 사용법](https://www.daleseo.com/java8-instant/)
- 타임존
    - [TimeZone, 어렵지 않게 이해하기](https://gngsn.tistory.com/167)
    - [자바스크립트에서 타임존 다루기 (1)*](https://meetup.nhncloud.com/posts/125)
- ZonedDateTime
    - [[Java8 Time API] ZonedDateTime 사용법](https://www.daleseo.com/java8-zoned-date-time/)
    - [[자바의 정석] Instant, LocalDateTime과 ZonedDateTime 차이](https://serina-the-best.tistory.com/35)
    - [Java Date & Time, 제대로 사용하기](https://gngsn.tistory.com/169)
- datetime vs timestamp
    - [[MySQL] DATETIME vs TIMESTAMP](https://yeonyeon.tistory.com/273)
    - [MySQL의 날짜 시간 타입(5.7 기준)](https://velog.io/@dion/mysql-5-7-datetime)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/30 java.time 패키지 내부는 어떻게 되어 있을까?

## 💡java.time 패키지

자바8부터 날짜와 시간을 나타내는 여러 API가 `java.time` 패키지에 추가되었다.

| 패키지 | 설명 |
| --- | --- |
| java.time | 날짜와 시간을 나타내는 핵심 API 포함, 표준(ISO-8601)에 기초함 |
| java.time.chrono | 표준(ISO-8601)이 아닌 달력 시스템이 필요할 때 사용할 수 있는 API 포함 |
| java.time.format | 날짜와 시간을 파싱하고 포맷팅하는 API 포함 |
| java.time.temporal | 날짜와 시간을 연산하기 위한 보조 API 포함 |
| java.time.zone | 시간대(타임존)을 지원하는 API 포함 |

### 1. java.time의 주요 클래스[[참고](https://devdocs.programmers.co.kr/references/java/docs/api/java.base/java/time/package-summary.html)]

| 클래스 | 설명 |
| --- | --- |
| 날짜 및 시간 |  |
| LocalDate | 로컬 날짜를 표현 |
| LocalTime | 로컬 시간을 표현 |
| LocalDateTime | 로컬 날짜와 시간을 표현 |
| ZonedDateTime | 특정 타임존의 날짜와 시간을 표현 |
| Instant | 특정 시점의 타임 스탬프를 표현 |
| 간격 |  |
| Duration | 두 시간 사이의 간격을 초/나노초로 표현 |
| Period | 두 날짜 사이의 간격을 년/월/일 단위로 표현 |
| 타임존 |  |
| ZoneId | 타임존(시간대)를 표현(시간 차이를 타임존 코드로 표현) |
| ZoneOffset | 시차(UTC 기준으로 고정된 시간 차이)를 표현 |

### 2. java.time.format의 주요 클래스[[참고](https://devdocs.programmers.co.kr/references/java/docs/api/java.base/java/time/format/package-summary.html)]

| 클래스 | 설명 |
| --- | --- |
| DateTimeFormatter | 날짜, 시간 객체를 출력하고 파싱하기 위한 포맷터 |
| DateTimeFormatterBuilder | 날짜-시간 포맷터를 만드는 빌더 |

### 3. java.time.temporal의 주요 인터페이스[[참고](https://devdocs.programmers.co.kr/references/java/docs/api/java.base/java/time/temporal/package-summary.html)]

| 인터페이스 | 설명 |
| --- | --- |
| Temporal | date, time, offset 클래스들의 상위 인터페이스 |
| TemporalAdjuster | temporal 객체를 조정하기 위한 함수형 인터페이스 |

## Reference

- java.time 패키지
    - [[Java] 자바8의 새로운 날짜 관련 클래스들 - java.time 패키지 (LocalDate, LocalTime, LocalDateTime, ZonedDateTime)](https://scshim.tistory.com/251)
    - [[Java] Java에서 날짜, 시간(Date, Time) 다루기](https://heekim0719.tistory.com/406)

# https://github.com/WeeklyStudy/modern-java-in-action/issues/32 ZonedDateTime과 OffsetDateTime는 어떤 차이점이 있고 각각 어느 상황에 사용하면 좋을까?

## 💡OffsetDateTime

> 날짜, 시간, offset에 대한 정보를 포함하는 클래스(LocalDateTime + ZoneOffset)
> 
![Untitled (13)](https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/3f1e6f7d-eeea-4476-b3bb-a6694dc6a0c9)


## 💡ZonedDateTime

> 날짜, 시간, offset, zone에 대한 정보를 포함한 클래스(OffsetDateTime + ZoneRegion)
> 
> - `ZoneRegin` : ZoneId를 상속받은 클래스
>

![Untitled (14)](https://github.com/WeeklyStudy/modern-java-in-action/assets/48237976/b2f01320-0e15-4898-b7c8-96851c2f936b)

- DST(Daylight Saving Time)와 같은 서머타임 정보를 포함한다.
- `ZoneRules`를 통해 DST를 내부적으로 처리한다.

## 💡결론

- `OffsetDateTime` 은 타임존 정보가 없고 `ZonedDateTime` 은 타임존 정보가 있는 것이 가장 큰 차이다.
- 데이터베이스나 네트워크 통신에는 `OffsetDateTime` 을 사용하고 글로벌 서비스에서는 `ZonedDateTime` 을 사용하자.

## Reference

- ZonedDateTime vs OffsetDateTime
    - [Java 8 ZonedDateTime vs OffsetDateTime 어떤 상황에서 쓰는게 적합한가?*](https://sujl95.tistory.com/86)
    - [(Java8) 날짜와 시간 API](https://perfectacle.github.io/2018/09/26/java8-date-time/#OffsetDateTime)
    - [OffsetDateTime, ZonedDateTime](https://authentication.tistory.com/43)
