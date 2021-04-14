---
title: "SLF4J"
tags:
  - logging
  - external_lib
draft: false
---

**SLF4J** - Simple Logging Facade - фасад, позволяющий одинаково работать с различными API для [логирования](../logging.md).

Необходимость создания такого фасада была продиктована наличием большого числа библиотек для логирования, имеющих схожий функционал, но с различным API. SLF4J является оберткой над всеми этими библиотеками и позволяет работать с ними единообразно.

Сам SLF4J предоставляет только интерфейс для логирования и не имеет реализации.

SLF4J поддерживает следующие библиотеки для логирования:
- [Logback](logback.md) - от того же создателя. Не имеет дополнительных оберток и адаптеров, а потому работает с SLF4J быстрее, чем другие библиотеки
- Log4j - предтеча всех библиотек для логирования
- [JUL](../java/java_util_logging.md) - стандартная Java реализация
- Simple - самая простая реализация, логирующая только `INFO` и выше в `System.err`. Используется в приложениях, где важен объем итогового jar.
- другие

## Использование

Пользоваться SLF4J невероятно просто: в классе, где требуется залогировать что-либо, нужно создать логгер с помощью фабрики, после этого можно им пользоваться в свое удовольствие:

```java
public class MyService {
    // Создание логера. Сделать его приватной константой - хорошая идея
    private static final Logger log = LoggerFactory.getLogger(MyService.class);
  
    public void doSomething(Long id) {
        log.trace("Detailed description of programm step");
        log.debug("Use it only for debugging");
        log.info("Important info about some step. Id = `{}`", id);
        log.warn("Something bad was happen with id `{}`, but not critical", id);
        log.error("Illegal behaviour of programm");
    }
}
```

Для вставки значений переменных в логируемый текст используются фигурные скобки `{}`. Порядок появления фигурных скобок соответствует порядку аргументов в логирующем методе.

### Fluent API

С выходом Java 8 и появлением лямбда-выражений стало возможным передавать в логгер не аргументы, а суплаеры их поставляющие:
```java
log.atInfo()
    .addArgument(() -> supplyArg())
    .log("Log text with lazy supplied argument = {}");
```
К сожалению, этот функционал доступен только в версии v.2.0.0, которая пока не является стабильной (и непонятно когда станет).

---
## Версии

По состоянию на апрель 2021 г. библиотека кажется заброшенной.
Последний коммит в репозитории был сделан в феврале 2020 г.
Релиз 2.0.0 был анонсирован в середине 2019 г. и до сих пор находится в альфе.

Кажется, что пора мигрировать на log4j2.

---
## Интеграция с [Lombok](lombok.md)

Создавать вручную Logger в каждом классе, где требуется что-либо залогировать, довольно утомительно, а простая копипаста не помогает.
Чтобы решить эту боль, в Lombok была добавлена аннотация `@Slf4j`.
После ее установки над классом в нем генерируется логгер по всем правилам.

С использованием аннотации:
```java
@Slf4j
public class MyService {
    public static void main(String... args) {
        log.error("Something else is wrong here");
    }
}
```

Без использования аннотации:
```java
public class MyService {
    private static final Logger log = LoggerFactory.getLogger(MyService.class);
  
    public static void main(String... args) {
        log.error("Something else is wrong here");
    }
}
```

---
## Подключение библиотеки
Для подключения библиотеки с помощью Maven необходимо добавить зависимость какой-либо библиотеки, поддерживающей SLF4J нативно (`slf4j-api` будет подтянут транзитивно):
```xml
<dependency> 
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

При использовании ненативной библиотеки для логирования, нужно подключить к проекту зависимость адаптера:
```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-to-slf4j</artifactId>
    <version>2.13.3</version>
</dependency>
```


---
## К изучению

- [X] Официальный сайт: http://www.slf4j.org/manual.html
- [X] Репозиторий: https://github.com/qos-ch/slf4j/