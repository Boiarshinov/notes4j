---
title: "Thymeleaf"
tags:
  - web
  - external_lib
draft: true
---

**Thymeleaf** - это шаблонизатор для Java-приложений, использующийся для создания html-верстки и ее заполнения контентом из Java-приложений.

Thymeleaf является аналогом [JSP](../jsp/jsp.md). Преимуществом Thymeleaf является то, что он тесно интегрирован со Spring и то, что он может использоваться не только в веб-приложениях, но и для генерации различных документов:

- шаблоны Email
- шаблоны CSV
- шаблоны PDF

Для использования возможностей Thymeleaf в HTML используются кастомные теги, подобно тому, как это делается в JSP.

При создании проекта с помощью Spring Initializr в файловой системе проекта генерируется папка `src/main/resourses/templates`. Шаблоны Thymeleaf должны лежать именно в ней. CSS-файлы должны лежать в также сгенерированной папке `src/main/resourses/static`.

---
## Теги HTML

Для использования кастомных тегов в html-верстке необходимо в теге `html` указать:
```html
<html xmlns:th="http://www.thymeleaf.org">
```

Thymeleaf добавляет к стандартному HTML ряд расширений, позволяющих манипулировать данными, получаемыми из Java-кода.

Во многих стандартных тегах HTML можно вставлять специальные атрибуты:

- `th:text` - для встраивания строк, полученных из Java-кода
- `th:each` - для итерирования по элементам коллекции, полученной из Java-кода

```html
<tr th:each="employee : ${employees}">
    <td th:text="${employee.firstname}"/>
    <td th:text="${employee.lastname}"/>
    <td th:text="${employee.email}"/>
</tr>
```

- `th:action` - ресурс куда будут отправлены данные
- `th:object` - ссылка на атрибут модели
- `th:field` - привязывает значение формы к полю атрибута модели

Для получения данных из Java-кода используется EL вида `${<attributeName>}`.

---
## Интеграция со Spring

Thymeleaf тесно интегрирован со Spring. Для того чтобы использовать возможности Thymeleaf в Spring Boot приложениях необходимо подключить к проекту следующую зависимость:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

Таким образом Spring MVC будет использовать шаблоны для генерации HTML, отдаваемого приложением.

<mark>Для передачи в верстку параметров их необходимо записать в</mark>

---
## К изучению

- [X] Курс по Spring и Hibernate на Udemy (в части касающейся): https://www.udemy.com/course/spring-hibernate-tutorial