# Boundaries

> ​	서드파티 패키지나 오픈소스를 사용할 때, 미리 테스트 하고 우리의 내부 코드와 깨끗하게 통합해야 한다.
>



## 서드파티 코드 사용하기

- 인터페이스를 "제공하는" 입장과 "사용하는" 입장 사이에는 필연적인 긴장감이 존재한다.
- "제공하는" 입장에서는 좀 더 다양한 환경에서 좀 더 많은 사용자가 사용할 수 있도록 다양한 사용성을 지향한다.
- "사용하는" 입장에서는 그들의 사용성에 맞는 specific한 인터페이스를 원한다.
- 이것을 "경계에서의 긴장"이라 부른다.



## 경계를 탐험하고 공부하기

- 서드파티 코드를 사용할 때, 우리는 적어도 우리가 사용할 코드에 대해서는 테스트를 할 필요가 있다.
- 곧바로 서드파티 코드를 사용하지 말고, 그 코드를 이해하기 위해 테스트를 작성할 수 있다.



## log4j 공부하기

```python
 // 1.
    // 우선 log4j 라이브러리를 다운받자.
    // 고민 많이 하지 말고 본능에 따라 "hello"가 출력되길 바라면서 아래의 테스트 코드를 작성해보자.
    @Test
    public void testLogCreate() {
        Logger logger = Logger.getLogger("MyLogger");
        logger.info("hello");
    }

    // 2.
    // 위 테스트는 "Appender라는게 필요하다"는 에러를 뱉는다.
    // 조금 더 읽어보니 ConsoleAppender라는게 있는걸 알아냈다.
    // 그래서 ConsoleAppender라는 객체를 만들어 넣어줘봤다.
    @Test
    public void testLogAddAppender() {
        Logger logger = Logger.getLogger("MyLogger");
        ConsoleAppender appender = new ConsoleAppender();
        logger.addAppender(appender);
        logger.info("hello");
    }

    // 3.
    // 위와 같이 하면 "Appender에 출력 스트림이 없다"고 한다.
    // 이상하다. 가지고 있는게 이성적일것 같은데...
    // 구글의 도움을 빌려, 다음과 같이 해보았다.
    @Test
    public void testLogAddAppender() {
        Logger logger = Logger.getLogger("MyLogger");
        logger.removeAllAppenders();
        logger.addAppender(new ConsoleAppender(
            new PatternLayout("%p %t %m%n"),
            ConsoleAppender.SYSTEM_OUT));
        logger.info("hello");
    }
    
    // 성공했다. 하지만 ConsoleAppender를 만들어놓고 ConsoleAppender.SYSTEM_OUT을 받는건 이상하다.
    // 그래서 빼봤더니 잘 돌아간다.
    // 하지만 PatternLayout을 제거하니 돌아가지 않는다.
    // 그래서 문서를 살펴봤더니 "ConsoleAppender의 기본 생성자는 unconfigured상태"란다.
    // 명백하지도 않고 실용적이지도 않다... 버그이거나, 적어도 "일관적이지 않다"고 느껴진다.
    
    // 조금 더 구글링, 문서 읽기, 테스트를 거쳐 log4j의 동작법을 알아냈고 그것을 간단한 유닛테스트로 기록했다.
// 이제 이 지식을 기반으로 log4j를 래핑하는 클래스를 만들수 있다.
// 나머지 코드에서는 log4j의 동작원리에 대해 알 필요가 없게 됐다.

public class LogTest {
    private Logger logger;
    
    @Before
    public void initialize() {
        logger = Logger.getLogger("logger");
        logger.removeAllAppenders();
        Logger.getRootLogger().removeAllAppenders();
    }
    
    @Test
    public void basicLogger() {
        BasicConfigurator.configure();
        logger.info("basicLogger");
    }
    
    @Test
    public void addAppenderWithStream() {
        logger.addAppender(new ConsoleAppender(
            new PatternLayout("%p %t %m%n"),
            ConsoleAppender.SYSTEM_OUT));
        logger.info("addAppenderWithStream");
    }
    
    @Test
    public void addAppenderWithoutStream() {
        logger.addAppender(new ConsoleAppender(
            new PatternLayout("%p %t %m%n")));
        logger.info("addAppenderWithoutStream");
    }
}
```



## "학습 테스트(Learning test)"는 긍정적이다


- 1. 공짜다
- 2. 메인 로직에 영향을 주지 않으며 서드파티 코드를 이해할 수 있다.
- 3. 서드파티 코드가 바뀔 경우 Learning test를 돌려 "아직 _우리가 필요한 기능_이 잘 동작하는지" 테스트할 수 있다.
- Learning test를 하던 말던, 경계 테스트는 새 버전으로의 이전에 도움을 준다.



## 아직 존재하지 않는 코드 사용하기

- 아직 개발되지 않은 모듈이 필요한데, 기능은 커녕 인터페이스조차 구현되지 않은 경우가 있을 수 있다.
- 하지만 우리는 이러한 제약때문에 우리의 구현이 늦어지는걸 탐탁치 않게 여긴다.
- 예시
  - 저자는 무선통신 시스템을 구축하는 프로젝트를 하고 있었다.
  - 그 팀 안의 하부 팀으로 "송신기"를 담당하는 팀이 있었는데 나머지 팀원들은 송신기에 대한 지식이 거의 없었다.
  - "송신기"팀은 인터페이스를 제공하지 않았다. 하지만 저자는 "송신기"팀을 기다리는 대신 "원하는" 기능을 정의하고 인터페이스로 만들었다. *[지정한 주파수를 이용해 이 스트림에서 들어오는 자료를 아날로그 신호로 전송하라]*
  - 이렇게 인터페이스를 정의함으로써 메인 로직을 더 깔끔하게 짤 수 있었고 목표를 명확하게 나타낼 수 있었다.(참조 1)

```python
public interface Transimitter {
    public void transmit(SomeType frequency, OtherType stream);
}

public class FakeTransmitter implements Transimitter {
    public void transmit(SomeType frequency, OtherType stream) {
        // 실제 구현이 되기 전까지 더미 로직으로 대체
    }
}

// 경계 밖의 API
public class RealTransimitter {
    // 캡슐화된 구현
    ...
}

public class TransmitterAdapter extends RealTransimitter implements Transimitter {
    public void transmit(SomeType frequency, OtherType stream) {
        // RealTransimitter(외부 API)를 사용해 실제 로직을 여기에 구현.
        // Transmitter의 변경이 미치는 영향은 이 부분에 한정된다.
    }
}

public class CommunicationController {
    // Transmitter팀의 API가 제공되기 전에는 아래와 같이 사용한다.
    public void someMethod() {
        Transmitter transmitter = new FakeTransmitter();
        transmitter.transmit(someFrequency, someStream);
    }
    
    // Transmitter팀의 API가 제공되면 아래와 같이 사용한다.
    public void someMethod() {
        Transmitter transmitter = new TransmitterAdapter();
        transmitter.transmit(someFrequency, someStream);
    }
}
```



## Clean한 경계

- 좋은 소프트웨어 디자인은 변경이 생길 경우 많은 재작업 없이 변경을 반영할 수 있는 디자인이다.
- Code at the boundaries needs clear separation and tests that define expectations.
- We manage third-party boundaries by having very few places in the code that refer to them. 
- **서드파티 바운더리는 적게, 명확하게, 테스트 후 사용하자.**
- Map 객체를 래핑하든 Adapter를 사용해 우리 입맛에 맞게 인터페이스를 변경하든, 코드는 보기 편해지고 경계 인터페이스를 일관적으로 사용할 수 있게 해주며 그들의 변경에도 유연하게 대응할 수 있게 해준다.



---

참고

https://github.com/Yooii-Studios/Clean-Code/blob/master/Chapter%2008%20-%20%EA%B2%BD%EA%B3%84.md

