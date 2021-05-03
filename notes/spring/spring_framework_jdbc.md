---
title: "Spring Framework JDBC"
tags:
  - spring
  - java_and_db
draft: false
---

# Spring Framework JDBC

**Spring Framework JDBC** - это часть Spring Framework и его кусочка Data Access.

Spring Framework JDBC предоставляет легковесную обертку над [JDBC](../java/jdbc.md). 
Эта обертка берет на себя все обязанности по работе с подключениями к БД, обработке стандартных JDBC исключений, работе с транзакциями и проч.

## `JdbcTemplate`

Центральной сущностью Spring Framework JDBC является [`JdbcTemplate`](jdbc_template.md). Про нее отдельная заметка.

## `DataSource`

`DataSource` - это интерфейс из JDBC, который призван скрывать детали получения соединения к БД. 
Обычно данный интерфейс реализуют пулы соединений, такие как: C3P0, Hikari, Apache Commons DBCP и др.

Сам Spring Framework JDBC не устанавливает какую-либо реализацию `DataSource` по умолчанию.
Однако при использовании Spring Boot (`spring-boot-starter-jdbc`) дефолтной реализацией является HikariCP.

## Исключения

<mark>TODO</mark>

### Преобразование исключений

В `JdbcTemplate` исключения, выбрасываемые классами JDBC (`SQLException`), преобразовываются в исключения из пакета `org.springframework.dao` с помощью одной из реализаций интерфейса `SQLExceptionTranslator`.

---
## К изучению

- [] Официальная документация: https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#jdbc