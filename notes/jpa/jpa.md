---
title: "JPA"
tags:
  - java_and_db
  - jpa
draft: false
---

**JPA** - Java Persistence API - спецификация Java, которая устанавливает интерфейс для ORM, работающих с реляционными базами данных.

Основными понятиями JPA являются сущности (Entity) и типы-значения (Value Type).

Для управления их жизненным циклом существует менеджер сущностей (Entity Manager).

JPA Criteria - API для написания динамических запросов к БД. Является аналогом QueryDSL.

JTA - Java Transaction API

---
## Основные реализации:

- [Hibernate ORM](../external_lib/hibernate_orm.md) - самая популярная реализация.
- Toplink - самая простенькая реализация от Oracle.

См.: https://stackoverflow.com/questions/17883971/which-provider-should-be-used-for-the-java-persistence-api-jpa-implemenation

---
## Нововведения в версиях JPA

### JPA 1.0 - 2006 г.

-

### JPA 2.0 - 2009 г.

- Автоматическая поддержка валидации полей сущности (в момент сохранения)
- Доступ к метаданным отображения сущностей
- Возможность переключения типа доступа к полям сущностей: напрямую через рефлексию или через методы доступа (аннотация `@Access`).

### JPA 2.1 - 2013 г.

- Появились аннотации `@Converter` и `@Convert`, позволяющие описывать применяемые конвертеры из Java-кода, вместо их описания в xml-файлах.
- Графы сущностей - EntityGraph

### JPA 2.2 - 2017 г.

- Поддержка новых классов времени, добавленных в Java 8.

* * *

## Взаимодействие с БД

Для обеспечения связи с БД и обращения с сущностями в JPA определены некоторые механизмы.

Основной компонент JPA - Persistent Unit - **Единица хранения**. По сути это объект представляющий собой базу данных.

Единицы хранения объявляются в xml-конфигурации.
<mark>//скопировать пример</mark>

В Java-коде для начала работы с единицей хранения, необходимо создать для нее фабрику менеджеров сущностей

```java
EntityManagerFactory factory = Persistence.createEntityManagerFactory("<persistentUnitName>");
```

Создание этой фабрики - очень дорогая операция, обычно она выполняется один раз при запуске приложения. При использовании энтерпрайзных фреймворков (Spring), созданием этой фабрики обычно занимаются они.

### Менеджеры сущностей

Менеджеры сущностей позволяют совершать стандартные CRUD операции и описывать с помощью [JPQL](jpql.md) различные операции с сущностями.

Подробнее о том, что такое сущности и об их жизненном цикле см. [JPA. Описание сущностей](jpa_entity.md).

Основные операции над сущностями:
#### `persist(Entity)`

- сохраняет сущность в БД (или делает запись в журнал коммита о том, что ее нужно сохранить)
- сохраняет в БД связанные сущности при наличии нужного типа каскадирования.
- переводит сущность в состояние *Managed*.

#### `refresh(Entity)`

- обновляет данные о сущности (на случай если она могла измениться за время транзакции)

#### `remove(Entity)`

- удаляет сущность в БД
- переводит сущность в состояние *Removed*.

#### `merge(Entity)`

- создает новую сущность на основе переданной, находившейся в состоянии *Detached* .
- переводит новую сущность в состояние *Persistent*.
- может копировать сущность.

---
## Транзакции

Каждый менеджер сущностей связан с экземпляром `EntityTransaction`, что позволяет управлять транзакциями.

Для работы с транзакциями в Spring приложениях зачастую используются [Spring Транзакции](../spring/transactional.md).

---
## Конфигурация

Конфигурация JPA осуществляется с помощью xml-файла `persistence.xml`. Этот файл должен лежать в директории `resources/META-INF`.

В конфиге объявляются единицы хранения и их настройки.
Для различных реализаций JPA могут быть свои индивидуальные тонкости настройки.

```xml
<persistence
        version="2.2"
        xmlns="http://xmlns.jcp.org/xml/ns/persistence"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence
        http://java.sun.com/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="helloWorldPU">
        <class>dev.boiarshinov.entities.Person</class>
        <exclude-unlisted-classes>true</exclude-unlisted-classes>

        <properties>

            <property name="javax.persistence.jdbc.driver" value="org.postgresql.Driver"/>
            <property name="javax.persistence.jdbc.url" value="jdbc:postgresql://localhost:5432/postgres"/>
            <property name="javax.persistence.jdbc.user" value="postgres"/>
            <property name="javax.persistence.jdbc.password" value="postgres"/>
            <!-- для автоматической генерации схемы БД при каждом старте приложения -->
            <property name="javax.persistence.schema-generation.database.action" value="drop-and-create"/>
            <property name="hibernate.show_sql" value="true"/>
        </properties>
    </persistence-unit>
</persistence>
```

Примечание - конфигурация JPA может сильно отличаться при совместном использовании с Hibernate, Spring Data и другими библиотеками. Но стандартизированный способ - именно этот.

---
## К изучению

- [X] Документация на пакет `javax.persistence`
- [X] Курс по Spring и Hibernate на Udemy (в части касающейся): https://www.udemy.com/course/spring-hibernate-tutorial
- [ ] Доходчиво про JPA на русском: https://easyjava.ru/data/jpa/
- [ ] Нововведения в версиях JPA: https://en.wikipedia.org/wiki/Java_Persistence_API#Version_history
- [ ] Книга "Java Persistence API и Hibernate" в части касающейся