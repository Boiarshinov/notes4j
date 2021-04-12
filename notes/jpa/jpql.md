---
title: "JPQL"
tags:
  - java_and_db
  - jpa
draft: false
---

**JPQL** - Java Persistance Query Language - язык запросов [JPA](jpa.md).

JPQL используется когда необходимо извлечь из репозитория выборку сущностей.

Одной из реализаций JPQL является [HQL](../external_lib/hql.md).

---
## Синтаксис

<mark>Скопировано с HQL, поправить</mark>

### Основные части запроса

- `from <Class name>` - указывается entity класс, из таблицы которого будут выбираться данные
- `where <Predicate>` - задает фильтрацию по элементам таблицы
```java
List<Student> doeStudents = entityManager
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
Query<Stident> query = entityManager.createQuery("from Student s where s.lastName = :studentLastName");
```

Затем, перед выполнением запроса необходимо передать значение переменной в запрос с помощью метода `setParameter()`

```java
query.setParameter("studentLastName", "Doe");
```

---
## Пагинация

Для того чтобы извлечь большой массив данных из репозитория по частям, используется [пагинация](../spring/pagination_and_sorting.md). В JPA используется паттерн offset - limit.

Отступ и размер выборки задаются при построении запроса с помощью специальных методов: `setFirstResult()` и `setMaxResults()`

```java
List<Student> studentsPage = entityManager.createQuery("select s from Student s orderBy s.id asc", Student.class)
    .setFirstResult(5)
    .setMaxResults(10)
    .getResultList();
```

Крайне рекомендуется в таких запросах явно прописывать способ и направление сортировки.

Если запрашиваемые сущности требуют JOIN FETCH с другими таблицами, то Hibernate сначала загрузит все записи в память, а потом будет осуществлять пагинацию внутри JVM. Это ведет к существенному оверхеду и просадкам по скорости работы программы.

Поэтому для увеличения быстродействия рекомендуется разбивать подобные запросы на два запроса: один для извлечения идентификаторов основных сущностей с учетом пагинации, и второй для извлечения искомых сущностей по внешнему ключу.

```java
List<Long> studentIds = entityManager.createQuery("select s.id from Student s orderBy s.id asc", Long.class)
    .setFirstResult(5)
    .setMaxResults(10)
    .getResultList();

List<Student> students = entityManager.createQuery("select distinct s from Student s "
    + "LEFT JOIN FETCH s.courses where a.id in (:studentIds)");
    .setParameter("studentIds", studentIds)
    .getResultList();
```

---
## К изучению

- [ ] Введение в JPQL: https://easyjava.ru/data/jpa/java-persistence-query-language/
- [ ] Видео про пагинацию в JPA: https://www.youtube.com/watch?v=p_sFYoJ4A_E&ab_channel=ThorbenJanssen