## 문제 1

책에서는 '기존 값을 변화시키는 데 집중했던 고전적인 객체지향에서 벗어나 함수형 프로그래밍으로 다가섰다는 것이 자바 8의 가장 큰 변화고, 이를 통해 자바에서는 두 가지 프로그래밍 패러다임의 장점을 모두 활용할 수 있게 되었다.'고 한다.

그럼 두 패러다임(함수형 프로그래밍, 객체지향 프로그래밍)의 장단점과 용도는 무엇일까?

## 답변 1

- 함수형 프로그래밍
    - 장점:
        - 참조투명성
            - 상태 변경을 피하고 **불변성**을 유지하여 코드의 안정성과 예측 가능성을 높일 수 있다.
            - 이로 인해 스레드 경쟁상태(교착상태)와 같은 **동시성** 문제도 줄일 수 있다.
        - 간결성
            - 함수 조합과 재사용을 통해 간결한 코드 작성이 가능하다.
        - 확장성
            - **동시성 및 병렬**을 지원하여 멀티코어 및 분산 환경에서 높은 확장성을 제공한다.
    - 단점:
        - 메모리 사용량 및 성능 저하 문제
            - 특히나 함수의 지역변수를 담아두는 스택영역이 충분치 않은 상황에서 함수형 프로그래밍을 남용하면 스택오버플로우가 발생할 수 있으니 주의해야 한다.
    - 용도:
        - 방대한 데이터를 빠르게 계산해서 병렬적, 안정적으로 처리하고자 할 때 유용하다.
      
```
💡 참조 투명성이란?
어떤 값으로 어떤 함수를 호출하면 언제든 같은 결과를 얻는다.
즉, 함수가 자신의 결과를 변화시키는 전역 상태를 변경하면 안된다는 것을 뜻한다.
```
    
    
- 객체지향 프로그래밍
    - 장점:
        - 캡슐화
            - 캡슐화와 정보 은닉을 통해 코드의 모듈화와 보안성을 높일 수 있다.
        - 다형성
            - 상속, 다형성 등을 통해 코드 재사용성과 확장에 유용하다.
                - 다형성은 비결합된(Strongly decoupled) 시스템을 만들어주기 때문에 바람직하다. 설계된 구조의 경계 사이에서 종속성이 역전될 수 있게끔 해주기 때문이다.
                - 그렇게 되면 모킹이나 가짜 객체들을 사용해 테스트도 가능하고, 다른 모듈들에 영향을 주지 않으면서도 수정될 수 있게 된다.
    - 단점:
        - 객체 생성 비용
            - 객체지향 프로그래밍은 객체를 생성하고 초기화하는 과정이 필요하므로 성능 측면에서 비용이 발생할 수 있다.
        - 상태 변이
            - 객체의 상태가 변경될 수 있어서 여러 스레드 간의 동기화와 관련된 문제가 발생할 수 있다.
            - 이로 인해 예기치 않은 버그가 발생할 수 있다.
        - 다중상속 (C++과 달리 자바는 내부적으로 구현이 불가하도록 막아두었기 때문에 해당되지 않음)
            - 다이아몬드 문제
    - 용도:
        - 현실 세계의 객체와 객체들 간의 상호작용을 모델링하는 데 적합하고, 객체의 속성과 행위를 조합하여 복잡한 시스템을 구축하는 데 사용된다.
- 결론
    - 자바8 이후 객체지향 프로그래밍의 캡슐화와 다형성, 그리고 함수형 프로그래밍의 참조투명성과 병렬 프로세싱을 조합하거나 선택하여 활용할 수 있게 되었다.
    - 하나의 언어가 반드시 하나의 패러다임을 지향해야 하는 것은 아니다.
    - 프로그래머는 원하는 목표에 따라 적절한 기법과 방법을 활용하여 만들어나가야 한다.
        - 예시로, UI 어플리케이션은 특성상 많은 상태 변화가 일어나기 때문에 순수 함수형 프로그래밍만 활용해서는 무리가 있다. 함수형과 반응형이 결합된 함수형 반응형 프로그래밍을 이용하거나, 객체지향 프로그래밍에서 함수형 프로그래밍의 특징을 결합하여 조금 더 안정적인 프로그램을 개발할 수 있다.

- 참고자료
    - [함수형 프로그래밍은 만능이 아니다](https://grapheople.tistory.com/8)
    - [함수형 프로그래밍 vs 객체지향 프로그래밍](https://mangsby.com/blog/programming/fp-vs-oop/)
    - [1. 자바는 왜 다중상속을 지원하지 않을까? - 다이아몬드 문제](https://nohriter.tistory.com/158)
    - [절차지향 vs 객체지향 vs 함수형](https://velog.io/@vipstar-_-/%EC%A0%88%EC%B0%A8%EC%A7%80%ED%96%A5-vs-%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-vs-%ED%95%A8%EC%88%98%ED%98%95)
    - [함수형프로그래밍이 대세다?! (함수형 vs 객체지향)](https://www.youtube.com/watch?v=4ezXhCuT2mw)

## 문제 2

일급 시민이란 무엇이고 자바 8에서 함수를 일급 시민으로 만들고자 한 이유는 무엇인가?

## 답변 2

- 일급시민이란
    - 프로그래밍 언어에서 이는 값으로서의 특징을 갖는 요소를 의미한다.
    - 즉, 변수에 할당하거나 데이터 구조에 저장하고, 함수의 매개변수로 전달하거나 반환값으로 사용할 수 있어야 한다.
- 함수를 일급시민으로 만들고자 한 이유
    - 함수형 프로그래밍은 프로그램을 함수들의 조합으로 작성하는 패러다임으로, 함수를 값으로 다루는 것이 핵심이다.
    - 즉, 자바 8에서는 함수형 프로그래밍의 장점(간결성, 재사용성, 불변성)을 활용하기 위해 함수 인터페이스(Predictate 등)와 람다 표현식을 도입하여 함수를 일급 시민으로 취급한 것이다.

```
💡 함수형 프로그래밍에서의 함수?
함수형 프로그래밍에서 사용하는 함수는 순수 함수(Pure Function)이다.


그리고 순수 함수란 부수효과(Side Effect)들을 제거한 함수이다.

- 부수효과(Side Effect)란 다음과 같은 변화 또는 변화가 발생하는 작업을 의미한다.
    - 변수의 값이 변경됨
    - 자료 구조를 제자리에서 수정함
    - 객체의 필드값을 설정함
    - 예외나 오류가 발생하며 실행이 중단됨
    - 콘솔 또는 파일 I/O가 발생함
```

```
💡 함수와 메서드의 차이? 
  Javascript에서 함수와 메서드의 차이점은 호출 방식에 따라 다르다. 함수를 호출하는 객체가 있는 경우 ‘메서드’, 함수를 호출하는 객체가 없는 경우 ‘함수’라고 칭한다.
```

- 참고자료
    - [메서드와 함수의 차이](https://developer-talk.tistory.com/534)
    - [함수형 프로그래밍](https://mangkyu.tistory.com/111)
    - [람다식과 함수형 인터페이스](https://mangkyu.tistory.com/113)
    

## 문제 3

자바8에서는 스트림을 통해 병렬화, 컬렉션 처리의 문제 등의 주제가 자주 나온다.

그렇다면 스트림과 컬렉션의 차이점은 어떤 차이점을 가지고 있을까?

## 답변 3
- 데이터 처리방식
    - 스트림
        - 스트림은 데이터의 흐름을 의미한다. 데이터에 어떤 계산을 할 것인지에 중점을 둔다.
        - 데이터 소스로부터 데이터를 가져와 중간 연산과 최종 연산을 이용하여 원하는 결과를 도출한다. 개발자가 중간 연산과 최종 연산을 체이닝하여 데이터 처리 과정을 표현하면, 스트림 API가 내부적으로 요소를 반복하며 연산을 수행한다.(내부 반복)
            - 내부 반복은 작업을 더 최적화된 다양한 순서로 처리할 수 있다.
    - 컬렉션
        - 컬렉션은 데이터의 모음을 의미한다. 어떻게 데이터를 저장하고 접근할지에 중점을 둔다.
        - 개발자가 명시적으로 반복문을 제어하며(외부 반복), 메모리에 저장된 요소들을 직접 접근하여 연산을 수행한다.
- 지연 처리
    - 스트림
        - Lazy : 중간 연산을 이용하여 스트림을 구성한 후, 최종 연산을 호출할 때에만 실제 연산을 수행한다.
    - 컬렉션
        - Eager : 즉시 모든 요소에 대한 연산을 수행한다.
- 참고자료
    - [스트림과 컬렉션의 차이](https://happy-coding-day.tistory.com/entry/%EC%9E%90%EB%B0%948-inAction-4-%EC%8A%A4%ED%8A%B8%EB%A6%BC-%EC%86%8C%EA%B0%9C)
    - [스트림과 컬렉션 비교](https://velog.io/@gwichanlee/%EC%8A%A4%ED%8A%B8%EB%A6%BCStream-kb2zjwfq#%E2%97%8E-%EC%BB%AC%EB%A0%89%EC%85%98%EA%B3%BC-%EC%8A%A4%ED%8A%B8%EB%A6%BC-%EB%B9%84%EA%B5%90)