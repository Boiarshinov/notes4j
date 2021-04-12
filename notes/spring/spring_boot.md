---
title: "Spring Boot"
tags:
  - spring
draft: false
---

Spring Boot включает в себя встроенный контейнер сервлетов. Это может быть:

- Tomcat - по умолчанию
- Jetty
- Netty - для реактивных приложений

### Spring Boot Initializer

Для создания Spring Boot приложений существует специальное веб-приложение (и плагин для Intellij IDEA), которое позволяет сконфигурировать Spring Boot приложение, создать необходимые начальные классы, выбрать нужные в проекте зависимости и пр.

---
## Дескриптор развертывания pom.xml

### Родительская зависимость

У всех Spring Boot приложений в pom.xml объявляется родитель
```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

В этом родителе объявлены:
- Версия Java, в которую будет компилироваться код (в текущих версиях - 8)
- Кодировка, использующаяся в проекте - UTF-8
- Плагин git-commit-id-plugin, позволяющий посмотреть у работающего приложения его версию и последний коммит текущей сборки.
- и пр.

### Библиотеки

Библиотеки, подключаемые Spring Boot, обычно имеют названия вида `spring-boot-starter-<word>`. Эти зависимости на самом деле являются набором взаимосвязанных библиотек с подходящими друг другу версиями.

У этих зависимостей обычно не указывается номер версии, потому что он зашит в родительской зависимости `spring-boot-starter-parent` и зависит от нее.

Например, `spring-boot-starter-data-jpa` содержит в себе Spring Data, [JPA](../jpa/jpa.md), [Hibernate ORM](../external_lib/hibernate_orm.md) и другие библиотеки с совместимыми между собой версиями.

### Плагин

В pom.xml, в том числе объявляется плагин `spring-boot-maven-plugin`, который позволяет собирать приложение в jar-файл.

---
## Настройки

Настройки Spring Boot приложения хранятся обычно в файле `application.properties` или `application.yml`. Второй вариант более предпочтительный, но пока недостаточно хорошо поддерживается IDE.

В этом файле можно прописывать свои собственные настройки, которые подтягиваются из приложения с помощью аннотации `@Value`.

Помимо этого Spring Boot поддерживает большое количество стандартных настроек:

### База данных
```yml
spring:
  liquibase:
    changeLog: classpath:db/changelog/db.changelog-master.xml
  datasource:
    url: jdbc:postgresql://localhost:5432/postgres
    username: postgres
    password: admin
    driverClassName: org.postgresql.Driver
  jpa:
    showSql: false
    hibernate:
      ddlAuto: none
    properties:
      hibernate:
        dialect: org.hibernate.dialect.PostgreSQLDialect

### Логирование

logging:
  level:
    org.hibernate: info
    org.springframework: error

### Внутренний HTTP-сервер

server:
  servlet:
    contextPath: /agent-cabinet
  port: 8081

### Безопасность

spring:
  security:
    user:
      name: admin
      password: admin
```
<mark>//Написать про это в заметке про Spring Security и дать здесь ссылку</mark>

### Мониторинг

Для мониторинга используется [Spring Boot Actuator](actuator.md).

---
## Профили

Так как приложение может быть развернуто на различных контурах, в которых могут отличаться требуемые настройки приложения (координаты БД, настройки security и логирования, URL интегрируемых сервисов и пр.), Spring Boot предоставляет возможность выбирать необходимые настройки приложения с помощью Spring профилей.

<mark>//Изучить и написать про Spring профили</mark>

---
### Точка входа

Spring Boot создает единую точку входа в приложение: это класс, помеченный аннотацией `@SpringBootApplication`, с методом `main`:

```java
@SpringBootApplication
public class MyApplication {
    public static void main(final String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

Эта аннотация является мета-аннотацией, объединяющей `@ComponentScan`, `@EnableAutoConfiguration` и `@Configuration`.
Подробнее об этих аннотациях в заметке [IoC-контейнер](ioc_container.md).

---
## К изучению

- [X] Курс Spring & Hibernate for Beginners на Udemy в части касающейся