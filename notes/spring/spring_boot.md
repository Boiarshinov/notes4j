---
title: "Spring Boot"
tags:
  - spring
draft: false
---

# Spring Boot

**Spring Boot** - это фреймворк, упрощающий разработку приложений, использующих Spring IoC.

Spring Boot включает в себя встроенный контейнер сервлетов. Это может быть:
- Tomcat - по умолчанию
- Jetty
- Netty - для реактивных приложений

В экосистеме Spring Boot большое количество артефактов `spring-boot-starter-...`. 
Эти артефакты по сути сборники различных библиотек из одной области.
Например: валидация, юнит-тесты, работа с БД и т.д.
В стартерах версии всех зависимости специально подобраны между собой, чтобы не было никаких конфликтов.

Также в Spring Boot по умолчанию включены инструменты для мониторинга состояния приложения и снятия с него различных метрик.

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
## Автоконфигурация
Существенная часть магии Spring Boot работает за счет автоконфигурации.
Spring Boot самостоятельно создает необходимые бины при наличии в classpath нужных зависимостей.
Например, при подключении библиотеки `spring-kafka` Spring Boot самостоятельно создаст бины `KafkaTemplate`, `ProducerFactory` и другие.

Делается это следующим образом.
В Spring Boot входит библиотека `spring-boot-autoconfigure`.
В этой библиотеке лежит огромное количество различных конфигурационных классов с названием `___AutoConfiguration`.
Также все эти конфигурационные классы перечислены в файле `/META-INF/spring.factories`, лежащим внутри указанной библиотеки.

Для того чтобы применялись только нужные для проекта конфигурации, а лишние не применялись, все автоконфигурационные классы помечены той или иной  `@Conditional` аннотацией.
Например, `KafkaAutoConfiguration`:
```java
@Configuration
@ConditionalOnClass(KafkaTemplate.class)
//...
public class KafkaAutoConfiguration {
  //...
}
```

Таким образом конфигурация будет применена только если в classpath будет класс `KafkaTemplate`.
А он появится в classpath при подключении соответствующей библиотеки.

---
## К изучению

- [ ] [Официальная документация](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/)
- [X] Курс Spring & Hibernate for Beginners на Udemy в части касающейся