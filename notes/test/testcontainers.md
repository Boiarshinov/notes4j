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

- [ ] Документация: https://www.testcontainers.org/
- [ ] Пример написания кастомного тестконтейнера: https://github.com/gAmUssA/testcontainers-hazelcast/tree/master