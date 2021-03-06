---
title: "JPA. Соответствие типов"
tags:
  - java_and_db
  - jpa
draft: false
---

Типы полей сущностей при сохранении в БД преобразуются в типы, существующие в БД. Рассмотрим соответствия типов.

## Числовые поля

<mark>дополнить</mark>

## Строки

<mark>дополнить</mark>

## Поля временного типа

Над полями, представляющими время, необходимо навесить специальную аннотацию `@Temporal`.

В аннотацию необходимо передать одно из значений перечисления `TemporalType`:

- `DATE` - переводит поле в sql-ный формат Date
- `TIME` - переводит поле в sql-ный формат Time
- `TIMESTAMP` - переводит поле в sql-ный формат Timestamp

Поле при этом должно быть представлено либо классом `Date`, либо классом `Calendar`.

```java
@Column(name = "created_at")
@Temporal(TemporalType.TIMESTAMP)
private Date createdAt;
```

В JPA 2.2 появилась поддержка классов времени, которые появились в [Date Time API](../java/time/datetime_api.md) с выходом Java 8.

| **Java-класс** | **SQL-тип** |
| --- | --- |
| `java.util.Date` | `timestamp` |
| `Calendar` | `timestamp` |
| `LocalDate` | `date` |
| `LocalTime` | `time` |
| `LocalDateTime` | `timestamp` |
| `OffsetTime` | `time_with_timezone` |
| `OffsetDateTime` | `timestamp_with_timezone` |
| `ZonedDateTime` | `timestamp` |
| `Duration` | не поддерживается |
| `Instant` | не поддерживается |

Для настройки этих временных типов в сущностях не требуется указывать никаких аннотаций над их полями.

## Перечисления

Перечисления могут храниться в БД по своему строковому имени или по порядковому номеру (ordinal).

Для выбора варианта хранения необходимо над полем перечисления поставить аннотацию `@Enumerated` и передать ей один из двух вариантов, определенных перечислением `EnumType`:

- `ORDINAL` - хранение по порядковому номеру перечисления. Используется по умолчанию
- `STRING` - хранение по названию перечисления.

И тот, и другой вариант недостаточно безопасны. Например, в перечисление может быть добавлен новый элемент между имеющимися, тогда порядковые номера перечислений будут нарушены. В случае с названиями перечислений при переименовании одного из элементов перечисления в ходе рефакторинга имеющиеся в БД данные перестанут правильно отображаться в объекты.

Для того чтобы избежать всех этих проблем, следует использовать [JPA Конвертеры](jpa_converters.md).

---
## К изучению

- [X] Документация на пакет `javax.persistence`
- [ ] Поддержка Java 8 Time API: https://www.baeldung.com/jpa-java-time
- [X] Хранение временных типов в JPA 2.2: https://thorben-janssen.com/map-date-time-api-jpa-2-2/
- [ ] Книга "Java Persistence API и Hibernate" в части касающейся
- [ ] Хранение перечислений с помощью JPA: https://www.baeldung.com/jpa-persisting-enums-in-jpa
- [X] Костыль по хранению перечислений до JPA 2.1: https://habr.com/ru/post/77982/
- [X] Сопоставление классов Date Time API с типами данных SQL: https://www.oracle.com/technical-resources/articles/java/jf14-date-time.html