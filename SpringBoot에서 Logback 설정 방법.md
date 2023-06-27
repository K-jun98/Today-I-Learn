# `스프링에서 LogBack 설정 방법`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <property name="LOG_PATH" value="./logs"/>

    <!-- 콘솔 -->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>INFO</level>
        </filter>
        <encoder>
            <pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread] %C %msg %M %n</pattern>
        </encoder>
    </appender>
    <!-- 파일 -->
    <appender name="INFO_LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>WARN</level>
        </filter>
        <file>${LOG_PATH}/info.log</file>
        <append>true</append>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/info_${type}.%d{yyyy-MM-dd}.gz</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread] %logger %msg%n</pattern>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="console"/>
        <appender-ref ref="INFO_LOG"/>
    </root>
</configuration>
```

### `Console Appender 영역`

<img width="500" alt="1" src="https://github.com/prgrms-be-devcourse/springboot-basic/assets/101342145/291e26ef-9054-4963-8860-705f4b79563e">  

Appender 영역은 로그의 형태를 설정하고 어떤 방법으로 출력하지를 설정하는 곳  
위에 사진과 같은 상속구조를 갖고 있고 `ConsoleAppender`,`FileAppender`,`RollingFileAppender`,`SMTPAppender` 등등의 Appender가 존재함.

```xml

<appender name="console" class="ch.qos.logback.core.ConsoleAppender">
    <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
        <level>INFO</level>
    </filter>
    <encoder>
        <pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread] %C %msg %M %n</pattern>
    </encoder>
</appender>
```

위에는 콘솔에 로그를 남기는 ConsoleAppender를 사용하고 있으며 INFO 레벨 이상의 로그를 기록함.  
`ThresholdFilter`는 설정된 로그 레벨 이상이 발생한 경우에만 로그 메시지를 통과시킵니다.  
만약 Console에는 INFO 레벨의 로그만 출력하고 싶다면 아래처럼 `filter`를 변경해주고 `onMatch`,`onMismatch` 속성을 사용해주면 됩니다.

```xml

<appender name="console" class="ch.qos.logback.core.ConsoleAppender">
    <filter class="ch.qos.logback.classic.filter.LevelFilter">
        <level>INFO</level>
        <onMatch>ACCEPT</onMatch>
        <onMismatch>DENY</onMismatch>
    </filter>
```

### `FileAppender 영역`

파일로그는 `RollingFileAppender`구현체를 이용하고 있습니다. `RollingFileAppender`는 파일에 로그 메시지를 작성하는 기능을 제공하며
로그 파일을 관리하는 설정도 가능합니다. 이것을 `롤오버`라고 하는데 롤오버 Policy 에는 `TimeBasedRollingPolicy`(시간 기반 롤오버), `SizeBasedTriggeringPolicy`(크기
기반 롤오버) 등이 있습니다.

```xml

<file>${LOG_PATH}/info.log</file>
<append>true</append>
<rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
<fileNamePattern>${LOG_PATH}/info_${type}.%d{yyyy-MM-dd}.gz</fileNamePattern>
<maxHistory>30</maxHistory>
</rollingPolicy>
<encoder>
<pattern>[%d{yyyy-MM-dd HH:mm:ss.SSS}] [%-5level] [%thread] %logger %msg%n</pattern>
</encoder>
```

- 현재 파일은 루트 디렉토리에 `info.log`라는 이름으로 파일이 생성되며 `<append>true</append>`는 애플리케이션을 실행할 때 파일을 새로 생성  
  할 것인지 아니면 이전 파일을 덮어쓸것인지 결정합니다. true로 설정 했으므로 기존 로그파일에 덮어 기록합니다.
- `<fileNamePattern>${LOG_PATH}/info_${type}.%d{yyyy-MM-dd}.gz</fileNamePattern>` 은 파일을 기록하는 시간단위와 파일의 이름 패턴을 설정합니다.
    - file속성과 다른점은 file은 현재 활성화된 로그 파일의 이름이라면 `fileNamePattern`은 아카이브된 파일의 이름을 설정합니다.
- `%d`는 파일 기록 시간을 의미하며 현재는 일단위로 아카이브합니다. 만약 시간단위로 파일을 아카이브하고싶다면 `%d{yyyy-MM-dd_HH}`로 변경하면 됩니다.
- `<maxHistory>30</maxHistory>` 이 설정은 롤오버된 로그 파일을 얼마나 오래 보관할지 결정합니다. 이 경우에는, 롤오버된 로그 파일은 30일 동안 보관됩니다. 30일이 지나면 가장 오래된
  롤오버된 로그 파일이 삭제됩니다.
- `<encoder>` 속성 안에 있는 부분은 로그 출력의 layout을 정한다고 생각하면됩니다. 앞에는 로그 시간을 의미하고 다른 패턴은 아래에 정리하겠습니다.

| 패턴      | 적용           |
|---------|--------------|
| %d      | 로그 발생 시간     |
| %p      | 로그 레벨        | 
| %M      | 로그 발생 메서드 이름 |
| %C      | 로그 발생 클래스명   |
| %thread | 로그 발생 스레드명   |
| %m      | 로그 메시지       |
| %n      | 줄바꿈          |

### `Root 영역`

```xml

<root level="INFO">
    <appender-ref ref="console"/>
    <appender-ref ref="INFO_LOG"/>
</root>
```
`Root` 영역은 로그레벨과 적용 영역의 대한 설정입니다. level속성은 루트 로거의 로깅 레벨을 설정합니다.
`appender-ref` 태그는 루트 로거에 적용될 어펜더입니다. 위의 예제에서는 "console"과 "INFO_LOG"라는 이름의 두 개의 appender가 참조되고 있습니다.  
로그 메시지가 이 두 개의 appender에 의해 처리되어야 함을 의미합니다.  
만약 특정 클래스에는 다른 로그 레벨을 적용하고 싶다면 아래와 처럼 특정 클래스를 적용하면 됩니다.  
`<logger name="com.dev.MyClass" level="DEBUG"/>`
