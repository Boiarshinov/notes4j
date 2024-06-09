---
title: "Testcontainers"
tags:
  - test
  - external_lib
draft: false
---

Testcontainers - это open-source библиотека для Java, позволяющая проведения тестов поднимать различные сервисы в docker-контейнерах.

Часто тестконтейнеры используются для поднятия СУБД для компонентных и интеграционных тестов.

Уже существуют тестконтейнеры для самых популярных СУБД: Oracle, MySQL, PostgreSQL. Если же для какой-то системы нет тестконтейнера, его можно написать самому.


__Q__: Когда нужно использовать тестконтейнеры?

__A__: Мы рассуждали на эту тему с одним коллегой и пришли к такому выводу.
В тех случаях, когда используется реляционная база данных со сложными запросами к ней, которые могут строиться с помощью всевозможных QueryDSL, JOOQ, Criteria API и прочих генерилок SQL, тогда у нас есть необходимость протестировать, что сгенерированный SQL корректный и правильно выполнится на настоящей базе.
В тех же случаях, когда у нас используются key-value хранилища с простыми операциями put, get, delete, то использование тестконтейнеров может приносить больше неудобств, чем пользы.
Например, контейнер [Cassandra](../database/cassandra.md) стартует очень долго и ожиданием прогона юнит-тестов становится слишком утомительным.
То же самое касается [Kafka](../tools/kafka.md), в которой две основные операции - produce и consume суперпростые, и проще замокать, чем поднимать целый брокер.

---
## Кастомный тестконтейнер

<mark>//написать про то, как можно создавать свои тестконтейнеры с помощью `GenericContainer`</mark>

---
## Подключение зависимости

Для подключения тестконтейнеров к своему проекту необходимо добавить в pom.xml:
```xml
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>testcontainers</artifactId>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>junit-jupiter</artifactId>
    <scope>test</scope>
</dependency>
```

Для того чтобы воспользоваться существующим тестконтейнером для интересующего сервиса (например PostgreSQL) нужно добавить в pom.xml зависимость и для него:

```xml
<dependency>
      <groupId>org.testcontainers</groupId>
      <artifactId>postgresql</artifactId>
      <scope>test</scope>
</dependency>
```

При этом если не хочется указывать версию зависимости, то нужно будет добавить в dependencyManagement следующее:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.testcontainers</groupId>
            <artifactId>testcontainers-bom</artifactId>
            <version>1.15.2</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```


---
## К изучению

- [ ] [Официальная документация](https://www.testcontainers.org/)
- [ ] [Пример](https://github.com/gAmUssA/testcontainers-hazelcast/tree/master) написания кастомного тестконтейнера