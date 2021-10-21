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

Spring Framework JDBC обертывает все исключения JDBC (`SQLException`) в `DataAccessException`.

`DataAccessException` - это корневой класс всех исключений из Spring Framework JDBC. Класс является абстрактным и объявляет семейство непроверяемых исключений. 
Одним из преимуществ `DataAccessException` является то, что оно изолирует код приложения от вендор-специфичных исключений, которые предоставляются различными JDBC-драйверами.

Большинство исключений наследников `DataAccessException` являются невосстанавливаемыми - это значит, что отлавливать их не имеет смысла, каждое из них должно приводить к Internal Server Error. 
Но бывают и такие исключения, которые отловить необходимо. Например, при запросе одного объекта по id может быть выброшено исключение `EmptyResultDataAccessException`, если запись с таким id не найдена. 
В тех случаях, когда интерфейс репозитория возвращает `Optional`, нужно данное исключение отловить и вернуть `Optional.empty()`:
```java
public Optional<Cat> getById(long id) {
  try {
		final Cat cat = jdbcTemplate.queryForObject(
			"select * from cat where id = ?",
			catRowMapper(),
			id
		);
		return Optional.ofNullable( catEntity );
	} catch (EmptyResultDataAccessException ex) {
		return Optional.empty();
	}
}
```


### Преобразование исключений

Преобразование исключей происходит автоматически.

В `JdbcTemplate` исключения, выбрасываемые классами JDBC (`SQLException`), преобразовываются в исключения из пакета `org.springframework.dao` с помощью одной из реализаций интерфейса `SQLExceptionTranslator`.

Если в DAO классе есть голый JDBC код, то чтобы гарантировать обертывание всех `SQLException` нужно повесить над классом аннотацию `@Repository` (которая заодно еще и сделает класс спринговым бином).

---
## К изучению

- [ ] [Официальная документация](https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#jdbc)