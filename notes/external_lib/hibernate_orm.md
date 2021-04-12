---
title: "Hibernate ORM"
tags:
  - java_and_db
  - jpa
draft: false
---

Hibernate ORM - эта самая популярная реализация ORM на Java.

В основе ORM лежит понятие **Entity** - сущность. Это обычный Java класс (POJO), который может быть представлен с помощью сочетания атрибутов таблиц базы данных.

В основе работы с БД лежат два объекта, предоставляемых Hibernate:
SessionFactory - это синглтон объект, который:

- читает конфигурационный файл
- создает объекты сессий (Session)
- регистрирует сущности (Entities)

Session - обертка над JDBC, позволяющая сохранять в БД и извлекать оттуда объекты.

Сессия - недолговечный объект, после того как с ее помощью были произведены операции с БД, она отправляется к сборщику мусора.

SessionFactory является аналогом EntityManagerFactory из JPA, а Session - аналогом EntityManager.

## Сессия

Сессия - <mark>это</mark>

### Создание сессии

Сессия создается с помощью объекта класса `SessionFactory` путем вызова метода `getCurrentSession()`:
```java
Session session = sessionFactory.getCurrentSession();
```

### Методы

В классе `Session` объявлены следующие методы:

- CRUD:
    - `Serializible save(Entity)` - сохраняет сущность в БД, возвращает значение первичного ключа
    - `T get(Class<T>, Id)` - достает из БД данные по первичному ключу
    - `delete(Entity)` - удаляет указанную сущность из БД
- действия с транзакциями:
    - `Transaction beginTransaction()` - открывает транзакцию
    - `Transaction getTransaction()` - возвращает транзакцию. Потом ее можно закоммитить.
- другое:
    - `Query createQuery(String)` - создает кастомный запрос к БД. В метод передается строка, написанная на [HQL](hql.md).
    - `Query createQuery(String, Class)` - создает кастомный запрос к БД. В метод передается HQL запрос и класс, к которому будут приведены возвращенные объекты.

В Hibernate любые действия записи или чтения из БД должны находиться внутри транзакции. Поэтому необходимо открывать транзакцию до совершения действий с БД и закрывать после:

```java
Session session = sessionFactory.getCurrentSession();
session.beginTransaction();
final Student readStudent = session.get(Student.class, studentId);
session.getTransaction().commit();
```

## CRUD

CRUD - это стандартные операции работы с БД. Чтобы узнать как можно кастомизировать эти операции, добавив необходимые условия, см. соответствующий раздел в заметке [HQL](hql.md).

### Create

Внутри открытой транзакции, необходимо передать объекту сессии в метод `save()` сохраняемую сущность:

```java
session.save(new Student("Arty", "Boiar", "artybor@gmail.com"));
```

### Read

Для того чтобы достать сущность из БД, необходимо в метод `get()` сессии передать класс сущности, объекты которого мы будем читать, и идентификатор.
```java
Student student = session.get(Student.class, studentId);
```

### Update

Для обновления данных, необходимо достать их из БД и засеттить необходимые поля. Запись в БД будет произведена при коммите транзакции.
```java
Student student = session.get(Student.class, studentId);
student.setFirstName("John");
```

### Delete

Для удаления данных из БД, необходимо достать удаляемые данные, а затем вызвать у сессии метод `delete()`, передав в него извлеченный ранее объект:
```java
Student student = session.get(Student.class, studentId);
session.delete(student);
```

---
# Mapping

Mapping - это буква M в аббревиатуре ORM, означающая отображение объектов на базу данных.

Отображение может задаваться двумя способами:

- с помощью xml-файла (устаревший способ)
- с помощью аннотаций

## Отображение с помощью аннотаций

Класс, объекты которого должны храниться в базе данных, помечается аннотацией `@Entity`. Также необходимо указать в какой таблице будут храниться основные сведения об объекте, делается это с помощью аннотации `@Table`.

```java
@Entity
@Table(name - "student")
public class Student { /* */ }
```
У класса должен быть объявлен конструктор без параметров.

Среди полей класса должен быть указан первичный ключ с помощью аннотации `@Id`. Атрибуты таблицы БД сопоставляются с полями класса с помощью аннотации `@Column`.

```java
@Id
private Integer id;

@Column(name = "first_name")
private String firstName;
```

Примечание - допускается не указывать имена таблиц и атрибутов, если они совпадают с именами классов и полей с учетом code-style, но рекомендуется это делать, чтобы защититься от ошибок при рефакторинге (изменении имен).

Генерация первичного ключа производится по одной из [стратегий](../jpa/jpa_primary_key.md).

Также можно объявить свою собственную стратегию генерации первичного ключа. Для этого нужно реализовать интерфейс `IdentifierGenerator` и его методы.

Большинство упомянутых аннотаций существуют в двух пакетах:

1. `javax.persistence` - пакет [JPA](../jpa/jpa.md)
2. `org.hibernate.annotations` - пакет Hibernate

Это связано с тем, что Hibernate был разработан раньше JPA и имел все эти аннотации. Когда была создана спецификация JPA, Hibernate реализовал ее, но остались дублирующиеся аннотации. Рекомендуется во всех случаях использовать аннотации из пакета JPA.

---
# Конфигурация

Конфигурация Hibernate ORM производится с помощью xml-файла.
```xml
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
<!--        connection settings -->

        <property name="connection.driver_class">org.postgresql.Driver</property>

        <property name="connection.url">jdbc:postgresql://localhost:5432/postgres</property>

        <property name="connection.username">postgres</property>
        <property name="connection.password">postgres</property>

<!--        connection pool settings -->
        <property name="connection.pool_size">1</property>

<!--        sql dialect settings -->

        <property name="dialect">org.hibernate.dialect.PostgreSQL95Dialect</property>

<!--        sql queries logging settings -->
        <property name="show_sql">true</property>

        <property name="current_session_context_class">thread</property>
    </session-factory>
</hibernate-configuration>
```

Чтение конфигурационного файла обычно производится при старте приложения:
```java
SessionFactory sessionFactory = new Configuration()
    .configure("hibernate.cfg.xml")
    .addAnnotatedClass(Student.class)
    .buildSessionFactory();
```

Если не передать имя xml-файла, по умолчанию будет искаться файл с именем `hibernate.cfg.xml`.

---
## Подключение зависимостей

Для использования Hibernate ORM в своем проекте необходимо подключить зависимость. В Maven это делается так:

```xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>${hibernate.orm.version}</version>
</dependency>
```

Также необходимо подключить JDBC драйвер используемой СУБД. В качестве примера приведено подключение драйвера PostgreSQL:

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <version>${jdbc.postgres.version}</version>
</dependency>
```

---
## К изучению
- [ ] Курс по Spring и Hibernate на Udemy: https://www.udemy.com/course/spring-hibernate-tutorial
- [ ] Официальный туториал: https://docs.jboss.org/hibernate/orm/5.4/quickstart/html_single/