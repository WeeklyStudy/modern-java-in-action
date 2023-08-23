# Q. Java의 Instrant vs LocalDateTime

## Instant 클래스

- 불변 객체이며 타임스탬프 형태로 사용 ⇒ 인간이 읽을 수 없는 형
- Epoch 시간(1970 년 1월 1일 UTC 기준)으로 부터 경과한 시간을 나노초 단위로 저장
- 동기화된 시계 시스템 기반으로 하며, 시간대 변환이나 일광 절약 시간을 고려하지 않음
- 모든 지역에서 동일한 시간을 가지며 시간대 변환이 필요한 경우 ZonedId 사용
- 데이터베이스에서 시간을 저장하거나 타임스탬프를 다룰 때 주로 사용

## LocalDateTime

- 지역 시간을 나타냄
- 시간대를 고려하지 않은 날짜, 시간 정보 가지며 시간대 변환, 일광 절약 시간 고려하지 않
- 특정 지역의 날짜, 시간 나타내는데 사용, 해당 지역에서 사용되는 로컬 시간 정보 담음
- 지역에 따라 다른 시간을 나타내며, 시간대 변환이 필요한 경우에는 **`ZoneId`**를 사용
- **`LocalDateTime`** 객체는 불변 객체이며, 날짜와 시간 정보를 조작하고 다루는데 사용

## 차이점

- Instant 는 전 세계 동일한 시간 나타내는데 사용되며, LocalDateTime은 특정 지역의 로컬 시간을 나타냄
- Instant 는 타임스탬프 형태의 시간 정보를 저장하고 사용, LocalDateTime은 로컬 날짜와 시간 정보 저장하고 다룸
- Instant 는 글로벌 시간을 다루는데 사용, LocalDateTime은 특정 지역의 로컬 시간을 다루는데 사용

## 사용 예시

- Instant 사용
    1. **로그 및 타임스탬프 저장**: 로그 파일이나 데이터베이스에 이벤트가 발생한 시간을 저장할 때 `Instant`를 사용 ⇒ 시간대 고려하지 않기 때문
    2. **시간 차이 계산**: 서로 다른 두 시간 사이의 경과 시간을 계산할 때 `Instant`를 사용
        
        ⇒ 에포크를 기준으로 경과한 나노초를 저장하므로, 시간 차이를 계산하기에 유용
        
- LocalDateTime 사용
    1. **예약 시스템**: 특정 지역의 로컬 시간을 기준으로 예약을 관리할 때 `LocalDateTime`를 사용
        
        ⇒ 항공편이나 이벤트 예약 시스템에서 로컬 시간을 다루는 데 유용
        
    2. **웹 애플리케이션**: 로컬 시간을 표시하거나 입력받을 때 활용
        
        ⇒ 사용자의 지역에 따른 로컬 시간 정보를 표시하고 조작에 사용
        
    3. **일정 및 이벤트 관리**: 로컬 시간을 기준으로 한 일정 및 이벤트 관리 시스템에서 사용자의 시간대에 맞게 일정을 나타냄
    4. **시간대 변환**: `Instant`는 시간대 변환을 고려하지 않지만, `LocalDateTime`는 시간대 변환이 필요한 경우 `ZoneId`를 사용하여 변환 가능
    
    **Instant**는 글로벌한 시간을 나타내고 시간 차이를 계산할 때 사용하며, **LocalDateTime**는 로컬 시간을 다루거나 특정 지역의 날짜와 시간을 관리할 때 사용
</br>

# Q. Java.time 패키지의 내부는 어떻게 되어 있을까?

java.time 패키지는 여러 하위 패키지를 포함하고 있으며, 다양한 날짜 시간 처리 관련 기능 제공

`java.time`, `java.time.chrono`, `java.time.format`, `java.time.temporal`, `java.time.zone` 

## 1. java.time

Instant, LocalDate, LocalTime, LocalDateTime 등 주요 날짜 및 시간 클래스 제공하며, Duration, Period 같은 시간, 날짜 간격을 다루는 클래스도 포함되어 있음

- Classes : Clock, Duration, LocalDate, LocalTime, LocalDateTime, MonthDay, OffsetDateTime, OffsetTime, Period, Year, YearMonth, ZonedDateTime, ZoneId, ZoneOffset
- Enums : DayOfWeek, Month
- Exception : DateTimeException
    - 날짜 시간을 계산하는 동안 문제를 나타내는 예외

### 1.1 **ZoneId 클래스**

시간대를 나타내는 클래스로, 시간대의 식별자(예: "Asia/Tokyo")를 사용하여 시간대 정보를 제공

- ZoneOffset와 달리 변동이 있는 시간대에도 적용

### 1.2 ZoneOffset

시간대 변환이나 일광 절약 시간 없이 UTC와의 차이를 나타내는 역할로 오프셋 정보 표현에 사용되며 지역 시간과 UTC 사이의 고정 시간 차이 표현함

- 고정 오프셋 정보
    
    특정 시간대의 오프셋을 고정적으로 나타냄 
    
    - ZonedOffset.ofHours(5) ⇒ UTC 와 5시간 차이나는 오프셋 나타냄
- 시간 차이 계산
    - 특정시간을 UTC 시간으로 변환, 시간 간격 계산 가능
- ISO 8601 형식 사용
    
    ISO 8601 형식 오프셋 문자열 사용 ⇒ “+05:00”
    

```java
import java.time.ZoneOffset;

public class ZoneOffsetExample {
    public static void main(String[] args) {
        ZoneOffset offset = ZoneOffset.ofHours(2); // +02:00
        System.out.println("Zone Offset: " + offset);
        
        ZoneOffset utcOffset = ZoneOffset.UTC; // UTC 오프셋
        System.out.println("UTC Offset: " + utcOffset);
        
        ZoneOffset customOffset = ZoneOffset.of("+05:30"); // +05:30
        System.out.println("Custom Offset: " + customOffset);
    }
}
```

### 1.2 OffsetTime

고정 오프셋을 가지고 있는 시간을 다루는데 사용하며, 시간 정보와의 UTC와 시간 차이를 가리키는 오프셋을 조합하여 사용

⇒ 시간대 정보보다는 시간과 시간차이에 집중하고자 할 때 유용

- 고정 오프셋을 가진 시간 표현
    
    OffsetTime 은 시간 정보와 고정 오프셋을 가지며, 시간대 변환을 고려하지 않고 **지역 시간의 오프셋을 나타냄**
    
- 시간 연산 및 비교
    
    LocalTime 과 유사한 기능 제공, 시간 연산 및 비교 수행 가능 
    
- 오프셋 정보 사용
    
    ZoneOffset 클래스로 생성된 오프셋 정보 활용하여 시간 정보에 오프셋 적용
    

```java
import java.time.OffsetTime;
import java.time.ZoneOffset;

public class OffsetTimeExample {
    public static void main(String[] args) {
        OffsetTime offsetTime = OffsetTime.of(15, 30, 0, 0, ZoneOffset.ofHours(5)); // 15:30 +05:00
        System.out.println("OffsetTime: " + offsetTime);
        
        OffsetTime currentTime = OffsetTime.now(); // 현재 시간과 오프셋
        System.out.println("Current OffsetTime: " + currentTime);
        
        OffsetTime laterTime = offsetTime.plusHours(2); // 2시간 후
        System.out.println("Later OffsetTime: " + laterTime);
    }
}
```

## 2. java.time.chrono

여러 달력 시스템을 지원하기 위한 클래스와 인터페이스 제공하며 ISO 달력 이외에도 다른 달력 시스템을 다룰 수 있도록 확장성 제공

⇒ ISO 달력 외의 다양한 문화에 따른 달력 시스템 지원

- Interfaces : ChronoLocalDate, ChronoLocalDateTime, Chronology, ChronoPeriod, ChronoZonedDateTime, Era
- Classes : AbstractChronology, HijrahChronology, HijrahDate, IsoChronology, JapaneseChronology, JapaneseDate, JapaneseEra, MinguoChronology, MinguoDate, ThaiBuddhistChronology, ThaiBuddhistDate
- Enums : HijrahEra, IsoEra, MinguoEra, ThaiBuddhistEra

### 2.1 Chronology 인터페이스

다양한 달력 시스템을 구현하는데 사용되는 기본 틀을 정의하며, 해당 인터페이스 구현하는 클래스는 특정 달력 시스템에 맞게 날짜와 시간 처리 가능

## 3. java.time.format

다양한 날짜 및 시간 형식을 형식화하고  파싱하기 위한 클래스와 기능을 제공 ⇒ **`DateTimeFormatter`** 

- Classes: DateTimeFormatter, DateTimeFormatterBuilder, DecimalStyle
- Enums : FormatStyle, ResolverStyle, SignStyle, TextStyle
- Exception : DateTimeParseException

### 3.1 DateTimeFormatter 클래스

날짜와 시간을 원하는 형식의 문자열 변환하거나 문자열을 날짜, 시간으로 파싱하며 형식 지정자를 통해 포맷할 수 있음

### 3.2 DateTimeFormatterBuilder 클래스

DateTimeFormatter 객체를 생성하고 커스터마이징하는데 사용

여러 개의 서브 패턴을 조합하여 복잡한 형식 생성 가능

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.time.format.DateTimeFormatterBuilder;
import java.time.format.SignStyle;
import java.time.temporal.ChronoField;

public class CustomDateTimeFormatterExample {
    public static void main(String[] args) {
        LocalDateTime now = LocalDateTime.now();
        
        DateTimeFormatter formatter = new DateTimeFormatterBuilder()
            .appendValue(ChronoField.YEAR, 4)  // 4자리 연도
            .appendLiteral("-")
            .appendValue(ChronoField.MONTH_OF_YEAR, 2)  // 2자리 월
            .appendLiteral("-")
            .appendValue(ChronoField.DAY_OF_MONTH, 2)  // 2자리 일
            .appendLiteral(" ")
            .appendValue(ChronoField.HOUR_OF_DAY, 2)  // 24시간 형식의 2자리 시간
            .appendLiteral(":")
            .appendValue(ChronoField.MINUTE_OF_HOUR, 2)  // 2자리 분
            .appendLiteral(":")
            .appendValue(ChronoField.SECOND_OF_MINUTE, 2)  // 2자리 초
            .toFormatter();
        
        String formattedDateTime = now.format(formatter);
        System.out.println("Formatted: " + formattedDateTime);
        
        String dateString = "2023-08-18 15:30:00";
        LocalDateTime parsedDateTime = LocalDateTime.parse(dateString, formatter);
        System.out.println("Parsed: " + parsedDateTime);
    }
}
```

### 3.3 FormatStyle 열거형

날짜와 시간의 표준적인 형식 스타일을 나타내는 열거형으로 SHORT, MEDIUM, LONG, FULL 형식 제공

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;

public class FormatResolverExample {
    public static void main(String[] args) {
        LocalDateTime now = LocalDateTime.now();
        
        // FormatStyle을 사용하여 표준 스타일로 포맷
        DateTimeFormatter shortFormatter = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.SHORT);
        String shortFormatted = now.format(shortFormatter);
        System.out.println("Short Formatted: " + shortFormatted);
        
        DateTimeFormatter longFormatter = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.LONG);
        String longFormatted = now.format(longFormatter);
        System.out.println("Long Formatted: " + longFormatted);
    }
}
```

- **`shortFormatted`**: 짧은 형식으로 포맷하면 "yy-MM-dd HH:mm"와 같이 표시
- **`longFormatted`**:  긴 형식으로 포맷하면 "MMMM d, yyyy h:mm:ss a"와 같이 표시

### 3.4 ResolverStyle 열거형

날짜와 시간 필드의 해석 규칙을 제어하기 위한 열거형으로 STRICT, SMART, LENIENT 규칙 설정 가능

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.time.format.ResolverStyle;

public class FormatResolverExample {
    public static void main(String[] args) {
        LocalDateTime now = LocalDateTime.now();
    
        // ResolverStyle을 사용하여 파싱 규칙 설정
        DateTimeFormatter customFormatter = new DateTimeFormatterBuilder()
            .appendPattern("yyyy/MM/dd HH:mm:ss")
            .toFormatter()
            .withResolverStyle(ResolverStyle.LENIENT); // Lenient 파싱 규칙
        
        String dateString = "2023/08/18 15:70:00"; // 잘못된 분과 초
        LocalDateTime parsedDateTime = LocalDateTime.parse(dateString, customFormatter);
        System.out.println("Lenient Parsed: " + parsedDateTime);
    }
}
```

## 4. java.time.temporal

날짜와 시간 객체의 필드, 단위에 접근, 조작하는데 사용되는 클래스, 인터페이스 포함하며 날짜, 시간 객체의 여러 요소에 접근하여 값을 읽고 수정하는 기능 제공

⇒  **`TemporalUnit`**, **`TemporalField`**, **`TemporalAmount`** 등을 가지고 있음

- Interfaces : Temporal, TemporalAccessor, TemporalAdjuster, TemporalAmount, TemporalQuery, TemporalUnit
- Classes : IsoField, JulianFields, TemporalAdjusters, TemporalQueries, ValueRange, WeekFields
- Enums : ChronoField, ChronoUnit
- Exceptions : UnsupportedTemporalTypeException

### 4.1  Temporal **인터페이스**

날짜와 시간 객체들이 구현하는 인터페이스로서, 날짜와 시간 요소에 접근하고 조작하는 메서드를 정의

### 4.2 TemporalAccessor 인터페이스

- Temporal 인터페이스의 읽기 전용 버전으로, 날짜와 시간 객체의 값을 읽는 메서드를 제공
- LocalDate, LocalTime, LocalDateTime 등의 객체들이 구현

### 4.3 TemporalField **인터페이스**

- 단일 날짜와 시간 필드에 접근하고 조작하는 메서드를 정의
- ChronoField 열거형 상수들이 TemporalField 인터페이스의 구현체

### 4.4 **TemporalUnit 인터페이스**

- 날짜와 시간의 단위(년, 월, 일, 시간 등)를 나타내는 인터페이스
- **ChronoUnit** 열거형 상수들이 **TemporalUnit** 인터페이스의 구현체

## 5. java.time.zone

시간대 관련 정보, 규칙을 다루는 클래스와 기능 포함하는 패키지

⇒ ZoneId, ZoneOffset, ZoneRules 등 포함되어 있음

- Classes : ZoneOffsetTransition, ZoneOffsetTransitionRule, ZoneRules, ZoneRulesProvider
- Enums : ZoneOffsetTransitionRule.TimeDefinition
- Exceptions : ZoneRulesException

</br>

# Q. ZonedDateTime과 OffsetDateTime는 어떤 차이점이 있고 각각 어느 상황에 사용하면 좋을까?

- `ZonedDateTime`과 `OffsetDateTime`은 모두 `java.time` 패키지에서 제공되는 클래스로, 날짜와 시간을 나타내는 데 사용
- 시간대와 오프셋을 다루는 방식에 차이가 있음

### ZonedDateTime 클래스:

- `ZonedDateTime`은 시간대 정보를 포함하는 날짜와 시간을 나타냅니다.
- 특정 지역의 로컬 시간을 나타내며, 시간대 정보를 포함하여 로컬 시간과 시간대 정보를 함께 다룹니다.
- `ZoneId`를 사용하여 특정 지역의 시간대를 설정하며, 시간대 변환과 일광 절약 시간을 고려하여 시간을 조작합니다.
- 서로 다른 지역의 시간을 다룰 때 유용하며, 시간대 변환과 로컬 시간을 동시에 고려해야 하는 경우에 사용됩니다.
- 예를 들어 "Asia/Tokyo"나 "America/New_York"와 같은 시간대 정보를 포함합니다.

### OffsetDateTime 클래스:

- `OffsetDateTime`는 오프셋을 가지는 날짜와 시간을 나타냅니다.
- 로컬 시간에 고정된 오프셋을 사용하여 날짜와 시간을 표현합니다. 예를 들어 UTC와의 시간 차이를 의미하는 오프셋을 포함합니다.
- 일광 절약 시간이나 시간대 변환과는 관련이 없이 오프셋에 의해 정의된 시간을 나타냅니다.
- 시간대 변환보다는 특정한 오프셋의 시간을 다루는 데 사용됩니다.
- 예를 들어 "+05:00"이나 "-08:00"과 같은 오프셋을 가집니다.

### **차이점:**

- `ZonedDateTime`은 시간대 정보를 포함하여 특정 지역의 로컬 시간을 나타냅니다. `OffsetDateTime`는 오프셋을 가진 로컬 시간을 나타냅니다.
- `ZonedDateTime`은 시간대 변환과 일광 절약 시간을 고려하여 시간을 조작하는 데 사용됩니다. `OffsetDateTime`는 오프셋에 의해 정의된 고정된 시간을 다루는 데 사용됩니다.

두 클래스는 시간대나 오프셋을 다루는 방식에 따라 사용 목적이 다르며, 어떤 클래스를 선택하여 사용할지는 각각의 요구사항에 따라 결정해야 합니다.
