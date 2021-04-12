---
title: "Spring MVC Form Tags"
tags:
  - spring
  - web
draft: false
---

Spring MVC Form Tags представляют собой специальные теги, встраиваемые в html-верстку и генерирующие код. По сути они являются продолжением [JSTL](../jsp/jstl.md).

Для подключения библиотеки тэгов необходимо в начале html-документа добавить [директиву](../jsp/jsp.md)

```html
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
```

---
## Основные теги

### Тэг form

form создает формочку, с помощью которой можно присвоить значения полям модельного объекта. Внутри тэга указываются следующие атрибуты:

- `action` - эндпойнт, в который будут переданы данные из формы.
- `modelAttribute` - ключ объекта, имеющегося в модели, в поля которого можно записывать данные.

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<html>
<head>
    <title>Welcome page</title>
</head>
<body>
<form:form action="processForm" modelAttribute="student">
    First name: <form:input path="firstName" />

    Last name: <form:input path="lastName" />

    <input type="submit" value="Submit">
</form:form>
</body>
</html>
```

Intellij IDEA при этом помогает не ошибиться с объектами, записанными в модель, и их полями.

### Тэг input

Тэг input позволяет создать текстовое поле для ввода информации
Атрибуты:

- `path` - указывается поле модельного объекта, в которое будет записано значение.

Пример применения выше.

### Тэг select

Тэг select позволяет организовать выпадающий список с выбором вариантов и организовать передачу данных из выпадающего списка в код.

Атрибуты:

- `value` - значение, которое будет отправлено при выборе данного варианта.
- `label` - надпись, которая будет отображаться в выпадающем списке.

При этом можно вместо того чтобы хардкодить значения в списке выбора, задать их с помощью `Map<String, String` в Java-коде, добавить этот словарь в модель в качестве атрибута и получить весь список опций с помощью тега options

```html
    <form:select path="country">
        <form:options items="${countryOptions}" />
    </form:select>
```

### Тэг radiobutton

Тэг radiobutton позволяет создать радиокнопку. Написав несколько таких тегов подряд, можно реализовать выбор из множества вариантов одного значения. Выбранное значение будет отправлено в Java-код.

Атрибуты:

- `path` - ссылка на поле модельного объекта, которому мы присваиваем значения в форме.
- `value` - значение, которое будет присвоено полю при выборе данного варианта из множества радиокнопок.

```html
    Favorite Language:
    Java <form:radiobutton path="favoriteLanguage" value="Java" />
    C# <form:radiobutton path="favoriteLanguage" value="C#" />
    PHP <form:radiobutton path="favoriteLanguage" value="PHP" />
    Ruby <form:radiobutton path="favoriteLanguage" value="Ruby" />
```

Вместо того чтобы хардкодить значения можно задать их с помощью `Map<String, String` в Java-коде, добавить этот словарь в модель в качестве атрибута и получить весь список опций с помощью тега **radiobuttons**, указав это словарь в атрибуте items

```html
    Favorite Language:
    <form:radiobuttons path="favoriteLanguage" items="${favoriteLanguagesOptions}" />
```

### Тэг checkbox

Тэг checkbox позволяет создать чекбоксы, которые позволяют совершить множественный выбор.

Выбранные варианты будут записаны в массив или список.
Атрибуты:

- `path` - ссылка на поле модельного объекта, которому мы присваиваем значения в форме.
- `value` - значение, которое будет добавлено в массив / список.

```html
    Operating Systems:
    <form:checkbox path="operatingSystems" value="Linux" /> Linux
    <form:checkbox path="operatingSystems" value="Mac OS" /> Mac OS
    <form:checkbox path="operatingSystems" value="Windows" /> Windows
```

Чтобы подгрузить значения из Java-кода можно воспользоваться тэгом **checkboxes**, передав в него словарь `Map<String, String`.

---
## К изучению

- [X] Курс Spring & Hibernate for Beginners на Udemy