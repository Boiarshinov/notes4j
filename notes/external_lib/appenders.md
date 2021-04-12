---
title: "Appenders - Аппендеры"
tags:
  - logging
draft: false
---

Данная заметка посвящена аппендерам [Logback](../external_lib/logback.md).

---
## ConsoleAppender
Свойства:
- <mark>todo</mark>

---
## FileAppender
Свойства:

- `append` - устанавливает следует ли дозаписывать в указанный файл, если в нем были записи до старта приложения или сначала стереть из него все записи. По умолчанию true.
- `file` - имя файла, в который будут писаться логи. Если файла с таким именем не существует, то он будет создан.
- `encoder` -
- `prudent` - включает режим, при котором аппендер будет писать в файл, даже если одновременно в этот файл пишет кто-то еще. По умолчанию false.
- `immediateFlush` - устанавливает будет ли включена буферизация сообщений или нет. Буферизация ускоряет работу приложения, но существует возможность потери логов, находящихся в буфере при форс-мажорах. По умолчанию false.

Пример конфигурации аппендера:
```xml
<timestamp key="bySecond" datePattern="yyyy-MM-dd_HH_mm_ss" />
<appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <append>false</append>
    <file>logs/log-${bySecond}.log</file>
    <encoder>
        <pattern>%date %-5level %logger{0} [%file:%line] %msg%n</pattern>
    </encoder>
</appender>
```

---
## RollingFileAppender

Ролловер - прекращение записи логов в один файл и начало записи во вновь созданный при наступлении какого-либо события (достижение файлом определенного размера, по времени)

Свойства:

- `file` - см. выше
- `append` - см. выше
- `encoder` -
- `rollingPolicy` - указывает как должен происходить ролловер. У свойства есть атрибуты:
    - `class` - определяет класс, который будет осуществлять ролловер
- `triggeringPolicy` - указывает когда должен происходить ролловер.
- `prudent` - см. выше

```xml
<appender name="ROLLING_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">

    <append>true</append>
    <file>logs/rollingLogFile.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <fileNamePattern>logFile.%d{yyy-MM-dd}.log</fileNamePattern>
        <maxHistory>30</maxHistory>
        <totalSizeCap>1GB</totalSizeCap>
    </rollingPolicy>
    <encoder>
        <pattern>%date %-5level %logger{0} [%file:%line] %msg%n</pattern>
    </encoder>
</appender>
```

## TimeBasedRollingPolicy

Устанавливает RollingPolicy в зависимости от временных интервалов. Если применен этот класс, то TriggeringPolicy можно не прописывать.

Свойства:

- `fileNamePattern` - паттерн, по которому будут именоваться файлы.
- `maxHistory` - устанавливает максимальное количество архивов с логами. Необязательное свойство.
- `totalSizeCap` - ограничивает максимальный размер архивных логов. Самые старые архивы будут удаляться при превышении лимита. Необязательное свойство.
- `cleanHistoryOnStart` - если true, то все архивные логи будут удалены при рестарте приложения.

## SizeAndTimeBasedRollingPolicy

Устанавливает RollingPolicy в зависимости от временных интервалов и размера лог-файлов.

Свойства:

- те же, что и у предыдущего.
- `maxFileSize` - устанавливает максимальный размер файла с логами, при превышении которого будет совершен ролловер.

## FixedWindowRollingPolicy
<mark>todo</mark>

## SizeBasedTriggeringPolicy
Свойства:

- `maxFileSize` - устанавливает максимальный размер файла, при превышении которого будет происходить ролловер.
```xml
<triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
    <maxFileSize>5MB</maxFileSize>
</triggeringPolicy>
```

---
## DBAppender
Позволяет писать в базу данных
```xml
<configuration>
    <appender name="DB" class="ch.qos.logback.classic.db.DBAppender">
        <connectionSource class="ch.qos.logback.core.db.DriverManagerConnectionSource">
            <driverClass>com.mysql.jdbc.Driver</driverClass>
            <url>jdbc:mysql://host_name:3306/database_name</url>
            <user>username</user>
            <password>password</password>
        </connectionSource>
    </appender>
    ...
</configuration>
```

---
## К изучению
- [X] Официальная документация по аппендерам logback: http://logback.qos.ch/manual/appenders.html