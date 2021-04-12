---
title: "Tag Library Descriptor - TLD"
tags:
  - java_ee
  - jsp
  - web
  - deprecated
draft: true
---

**TLD** - Tag Library Descriptor - это файл с расширением .tld, в котором описываются кастомные теги, которые затем можно будет применить в [JSP](jsp.md) с использованием [JSTL](jstl.md).

TLD представляет собой [xml](../xml.md) с предопределенными тегами.

.tld файл может располагаться в корне папке WEB-INF, в ее поддиректории или в папке META-INF подключаемых библиотек.

---
## Структура TLD

- `uri` - уникальное имя. Это необязательно должно быть реальное расположение чего-либо.

```xml
<?xml version="1.0" encoding="ISO-8859-1" ?> <!-- Объявление XML -->
<!-- Заголовочный тег, содержит ссылку на схему и пр. -->
<taglib xmlns="http://java.sun.com/xml/ns/j2ee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd"
        version="2.0">

    <tlib-version>1.2</tlib-version> <!-- Обязательный тег, версия библиотеки -->
    <short-name>bla</short-name> <!-- Обязательный тег -->
    <uri>DiceFunctions</uri> <!-- Уникальное имя, которое прописывается в директивах JSP -->
    <function>
        ...
    </function>
    <tag>
        ...
    </tag>
</taglib>
```

---
## Подключение функций классов

Для подключения функции какого-либо Java класса необходимо описать ее в теге `<function>`

- `name` - имя функции. Задается произвольным, по этому имени функция затем будет вызываться в JSP.
- `function-class` - указывается класс, в котором находится метод (с пакетами, начиная от папки java)
- `function-signature` - указывается сигнатура метода (возвращаемый тип, название и типы аргументов)

```xml
<function>
    <name>roll</name>
    <function-class>model.Dice</function-class>
    <function-signature>int rollTheDice(int)</function-signature>
</function>
```

---
## Создание тегов
Теги объявляются в тегах `<tag>`

- `<description>` - описание тега. Необязательное поле
- `<name>` - название тега. Обязательное поле
- `<tag-class>` - класс, который реализует данный тег. Обязательное поле
- `<body-content>` - указывает, что должно быть в теле тега. Принимает четыре возможных значения: empty, scriptless, tagdependent, JSP,- что соответственно значит: пустой, без скриплетов, тело воспринимается как текст, со всеми возможностями JSP.
- `<attribute>` - объявляются атрибуты
    - `<name>` - имя атрибута
    - `<required>` - обязательность. Принимает значения true или false.
    - `<rtexprvalue>` - может ли атрибут быть объектом (кроме String).  Принимает значения true или false. Значение по умолчанию - false.
    - `<type>` - класс атрибута (используется, если rtexprvalue=true)

```xml
<tag>
    <description>random advice</description>
    <name>advice</name>
    <tag-class>package.MyClass</tag-class>
    <body-content>empty</body-content>

    <attribute>
        <name>user</name>
        <required>true</required>
        <rtexprvalue>true</rtexprvalue>
    </attribute>
</tag>
```

Также необходимо реализовать класс, который будет обрабатывать тег. Класс должен быть унаследован от `SimpleTagSupport`, в нем должны быть сеттеры для атрибутов и должен быть реализован метод `doTag()`.

---
## Класс `SimpleTagSupport`

Класс не используется напрямую, а должен реализовываться программистом для использования кастомных тегов.

Методы:
- `void doTag()` - главный метод, который должен быть переопределен программистом.
- `JspContext getJspContext()` - возвращает контекст JSP, который

---
## К изучению:

- [X] Глава 9 Head First Servlets and JSP