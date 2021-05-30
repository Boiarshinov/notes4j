---
title: "Нововведения в версиях Java"
tags:
draft: false
---

## Java SE

### Java 1.1 (1997 г.)
- Символьные потоки
- пакет `java.net`

### Java 1.4 (2002 г.)
- Цепочечные исключения
- Пакет NIO - New Input Output, в котором были добавлены буферы, каналы и наборы символов
- [Ассерты](assert.md)

### Java 5 - Tiger (2004 г.)
- [Перечисления](enum.md)
- [Аннотации](annotations.md)
- Автоупаковка и автораспаковка
- Future
- [Обобщения (Generics)](generics.md)

### Java 6 - Mustang (2006 г.)
- Класс Console

### Java 7 - Dolphin (2011 г.)
- try с ресурсами
- Групповой перехват исключений
```java
} catch (RuntimeException | IOException e) { ... }
```
- Окончательное повторное генерирование исключений
- String в switch для выбора ветки
- Запись данных в бинарном виде: 0b01010101
- пакет NIO.2 (new input output), в котором были устранены недостатки стандартных классов для работы с файлами
- Пустые аргументы у обобщенных типов в diamond
- Изменение метода substring() - теперь каждый раз при обрезке строки в памяти хранится новый массив символов. Программы стали кушать больше памяти, но были устранены проблемы с утечкой памяти.
- [Fork/Join фреймворк](multithreading/fork_join.md)

### Java 8 - Octopus (2014 г.) LTS
- [Лямбда-выражения](stream_and_lambda/lambda_expressions.md) и [функциональные интерфейсы](stream_and_lambda/functional_interface.md)
- [Стримы](stream_and_lambda/stream.md)
- Дефолтные методы в интерфейсах (были нужны для реализации стримов)
- Статические методы в интерфейсах (были нужны для реализации стримов)
- effective-final переменные и параметры методов
- [Date Time API](time/datetime_api.md) - новое API для работы со временем
- CompletableFuture
- Класс StringJoiner
- Оптимизация HashMap при большом количестве коллизий (подробнее см. [Map](collections/map.md))


### Java 9 (2017 г.)
- Compact Strings - теперь строки могут представляться в JMM не только как массив символов, но и как массив байт. Как следствие - увеличение производительности.
- Модульная система
- Reactive API
- Статические методы генерации `of()` в коллекциях
- Чтение из properties файлов в кодировке UTF-8
- javax.annotation выпилена из стандартного JDK
- Инкубатор нововведений - в нем предложенный API может динамично изменяться какое-то время перед тем как быть зафиксированным в одном из новых релизов.
- System.Logger - фасад для [логирования](../logging.md), облегчающий работу с различными логерами.
- Расширение API CompletableFuture
- из стандартной библиотеки был убран пакет jaxb - теперь его нужно подключать в качестве зависимости.

### Java 11 LTS (2018 г.)
- Java HTTP API
- Garbage Collector G1 теперь GC по умолчанию
- Оболочка для терминала JShell
- ZGC - масштабируемый сборщик мусора
- Shenandoah - сборщик мусора (портирован в Java 11 в 2020 г.)
- Добавление всяких разных хороших методов в класс String

### Java 12 (03.2019 г.)
- Shenandoah - экспериментальный сборщик мусора
- Улучшен G1
- Microbenchmark Suite - встроенный в Java бенчмарк

### Java 13 (2019 г.)
- Переписан Socket API с нативного кода на Java
- switch expression - превью
- Текстовые блоки - превью

### Java 14 (2020 г.)
- Рекорды - превью
- NPE с указанием метода, в котором было выброшено исключение - превью
- switch expressions - переделанный switch, возвращающий значение
- instanceOf с автоматическим кастом - превью
- API доступа к памяти других процессов

### Java 15 (2020 г.)
- Текстовые блоки
- ZGS релизнулся
- Sealed-классы - превью
- Hidden-классы
- Выпилен JS-движок Nashorn

### Java 16 (2021 г.)
- Рекорды

---

## Java EE

<mark>Вынести бы в отдельную заметку</mark>

### J2EE 1.2 (1999 г.)
- [Сервлеты](../servlets/servlet.md)
- [JSP](../jsp/jsp.md)
- EJB
- JMS
- RMI

Всего 10 спецификаций

### Java EE 5 (2006 г.)
- [JPA](../jpa/jpa.md) - Java Persistence API
- JSF
- JAXB - Java Architecture for XML Binding. Библиотека были выпилена из стандартной комплектации Java и вынесена в отдельный jar.

Всего 23 спецификации

### Java EE 6 (2009 г.)
- [CDI](../java_ee/cdi.md)
- [JAX-RS](../java_ee/jax_rs.md)
- [Bean Validation](../java_ee/bean_validation.md)

Всего 28 спецификаций

### Java EE 8 (2013 г.)
- JSON-P

Всего 31 спецификация

---

## К изучению
- [X] История версия Java SE: https://ru.wikipedia.org/wiki/%D0%98%D1%81%D1%82%D0%BE%D1%80%D0%B8%D1%8F_%D0%B2%D0%B5%D1%80%D1%81%D0%B8%D0%B9_Java_SE
- [X] История версий Java EE: https://ru.wikipedia.org/wiki/%D0%98%D1%81%D1%82%D0%BE%D1%80%D0%B8%D1%8F_%D0%B2%D0%B5%D1%80%D1%81%D0%B8%D0%B9_Java_EE
- [X] Подробный обзор нововведений, начиная с Java 8: https://javarush.ru/groups/posts/2547-iz-8-v-13-polnihy-obzor-versiy-java-chastjh-1
- [X] Видео с фичами Java SE, начиная с Java 8, от Немчинского (так себе): https://www.youtube.com/watch?v=p837jJ_sXjk&ab_channel=SergeyNemchinskiy