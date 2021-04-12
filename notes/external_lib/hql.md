---
title: "HQL"
tags:
  - java_and_db
  - jpa
draft: false
---

HQL - Hibernate Query Language - язык запросов Hibernate ORM

HQL запрос записывается в виде строки, которая передается на исполнение в метод класса `Session` - `createQuery()`.

---
## Синтаксис

### Основные части запроса

- `from <Class name>` - указывается entity класс, из таблицы которого будут выбираться данные
- `where <Predicate>` - задает фильтрацию по элементам таблицы
```java
List<Student> doeStudents = session
    .createQuery("from Student s where s.lastName = 'Doe'", Student.class)
    .getResultList();
```

- `update <Class name>` - указывается entity класс, в таблице которого будут изменены данные
- `set <Fields> = <Values>` - указываются изменяемые атрибуты и их новые значения

```java
session.createQuery("update Student set email='john.doe@foo.bar' where firstName = 'John'")
    .executeUpdate();
```

- `delete` - удаляет сущности, удовлетворяющие условию
```java
session.createQuery("delete from Student where firstName = 'John'")
    .executeUpdate();
```

### Работа с предикатами

- `<Predicate> or <Predicate>` - объединяет два предиката с помощью логической операции ИЛИ.
- `<Predicate> and <Predicate>` - объединяет два предиката с помощью логической операции И.
- `<attribute> like <Regex>` - возвращает true, если атрибут удовлетворяет регулярному выражению.
```java
final List<Student> doeStudents = session
    .createQuery("from Student s " +
        "where s.lastName = 'Doe' " +
        "or s.firstName = 'Martin'", Student.class)
    .getResultList();
```

### Переменные

Для использования переменных в HQL запросах необходимо записать имя переменной с двоеточием в начале
```java
Query<Stident> query = session.createQuery("from Student s where s.lastName = :studentLastName");
```

Затем, перед выполнением запроса необходимо передать значение переменной в запрос с помощью метода `setParameter()`
```java
query.setParameter("studentLastName", "Doe");
```

---
## CRUD

### Create

### Read

В объект сессии в метод `createQuery()` передается строка с select запросом и класс сущности, объекты которой мы хотим прочитать. Затем вызывается метод `getResultList()`:
```java
final List<Student> doeStudents = session
    .createQuery("from Student s where s.lastName = 'Doe' ", Student.class)
    .getResultList();
```

### Update

В объект сессии в метод `createQuery()` передается строка с update запросом. Затем вызывается метод `executeUpdate()`:
```java
session.createQuery("update Student set email='john.doe@foo.bar' where firstName = 'John'")
    .executeUpdate();
```

### Delete

В объект сессии в метод `createQuery()` передается строка с delete запросом. Затем вызывается метод `executeUpdate()`:
```java
session.createQuery("delete from Student where firstName = 'John'")
    .executeUpdate();
```

---
## К изучению
- [ ] Курс по Spring и Hibernate на Udemy: https://www.udemy.com/course/spring-hibernate-tutorial