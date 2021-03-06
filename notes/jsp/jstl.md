---
title: "JSTL"
tags:
  - java_ee
  - jsp
  - web
  - deprecated
draft: false
---

**JSTL** - Java Standard Tag Library - стандартная библиотека тегов.

Используется для того чтобы не применять скриплеты в [JSP](jsp.md).

JSTL разработан в рамках JSR-052.

---
## Подключение библиотеки в Maven
К зависимостям нужно добавить новый артефакт jstl
```xml
<dependency>
    <groupId>jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```

## Подключение в JSP

Для возможности использования тегов JSTL в JSP необходимо подключить библиотеку с помощью директивы
```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

---
## Теги основного назначения

- `c:out` - включает в JSP указанный текст, при этом можно контролировать отображение тегов, имеющихся в передаваемом тексте. Использование этого метода предотвращает *cross-site hacking*.
    - `value` - выводимый текст. Обычно здесь применяются [expression language](expression_language.md).
    - `escapeXml` - принимает значения true или false. Параметр устанавливает будут ли выводиться на печать теги текстовой разметки или нет. При true - будут выводиться, как символы. При false - теги будут исполнятся как теги. Дефолтное значение - true.
    - `default` - текст, переданный в данный атрибут, будет выводиться, если value вернет null.

```jsp
<b>Hello <c:out value='${user}' default='guest' />.</b>
```

- `c:set` - устанавливает значение атрибута. Если атрибута с указанным именем не существует, то он создается.
    - `var` - название атрибута
    - `value` - передаваемое значение. Если использовать EL, то можно передавать объекты любого типа.
    - `scope` - устанавливает область (сферу [атрибута](../servlets/webapp_attributes.md)). Дефолтное значение - `page`.
    - `target` - указывается ссылка на [java bean](../java/java_bean.md) или [мапу](../java/collections/map.md). Если передать что-нибудь другое, то контейнер выкинет исключение.
    - `property` - указывается ключ словаря или поле JavaBean. Если в JavaBean нет такого поля, то контейнер выкинет исключение.

```jsp
<c:set var="userLevel" scope="session" value="moder" />
<c:set target="${person}" property="name" >
    ${foo.name} <!-- Можно писать в тело тега вместо атрибута value -->
</c:set>
```

- `c:remove` - удаляет атрибут
    - `var` - название атрибута
    - `scope` - сфера атрибута. Если не указывать, то атрибут будет удален из всех сфер.
- `c:catch` - в тег можно обернуть код, который способен выкинуть ошибку. Тег работает подобно конструкции try-catch. Если в процессе выполнения кода, обернутого в c:catch будет выброшена ошибка, то код продолжит выполняться с закрывающего тега `c:catch`
    - `var` - имя переменной, в которую будет записана ссылка на исключение, если оно будет выброшено.

```jsp
<c:catch var="myException">
    <% int x = 1 / 0; %>
    You will never see this message
</c:catch>
But you will see this message
```

---
## Теги условий

- `c:if` - заходит в тело тега, если выполняется условие
    - `test` - здесь записывается условие, которое должно возвращать true или false. Обычно используются EL-выражения.

```jsp
<c:if test="${userType eq 'member'}" >
    <jsp:include page="inputCommentSection.jsp"/>
</c:if>
```

- Аналог switch
    - `c:choose` - двойной тег, определяет границы описания кейсов
    - `c:when` - кейс с условием
        - `test` - здесь записывается условие
    - `c:otherwise` - дефолтный кейс

```jsp
<c:choose>
    <c:when test="${vovanMainProperty == 'loh'}">
        ...
    </c:when>
    <c:when test="${vovanMainProperty == 'pidor'}">
        ...
    </c:when>
    <c:otherwise>
        Impossible
    </c:otherwise>
</c:choose>
```

---
## Итерирующие теги

- `c:forEach` - итерирует массив, коллекцию, словарь или даже String со значениями, разделенными запятыми.
    - `var` - устанавливает имя переменной, в которую будет записан i-ый элемент массива
    - `items` - ссылка на массив. Здесь обычно применяется выражение EL.
    - `varStatus` - устанавливает имя переменной, которая хранит метаданные цикла (значение i)
    - `begin` - устанавливает начало итерации
    - `end` - устанавливает конец итерации
    - `step` - устанавливает шаг итерирования

```jsp
<table>
    <c:forEach var="movie" items="${movieList}" varStatus="movieLoopCount" >
        <tr>
            <td>${movieLoopCount.count}</td> <!-- Выводит значение итератора -->
            <td>${movie}</td> <!-- Выводит значение итерируемой коллекции -->
        </tr>
    </c:forEach>
</table>
```

---
## Теги, связанные с URL

- `c:import` - динамически включает в JSP html, находящийся по указанному URL (может быть как внутри проекта, так и внешняя ссылка). Энкодинг url при отключенных [cookies](../servlets/cookies.md) происходит автоматически.
    - `url` - ссылка либо на внутренний адрес страницы, либо на внешний.
- `c:param` - добавляет параметр к запросу (аналог *jsp:param*). Используется в составе тега c:import, чтобы передать в подключаемую страничку некоторые параметры.
    - `name` - имя параметра.
    - `value` - значение параметра.

```jsp
<c:import url="/header.jsp" >
    <c:param name="subTitle" value="Vovan - lox" />
</c:import>
```

- `c:url` - добавляет к URL информацию о сессии. Но не энкодирует ее. Для энкодирования используется тег `c:param` (см. выше).
    - `value` - URL
    - `var` - объявляется имя переменной, которая содержит ссылку на созданный URL. Используется, когда нужно будет использовать данный URL в дальнейшем коде.

```jsp
<a href="<c:url value='/inputComments.jsp'>">click here</a>
<с:url value="/inputComments.jsp" var="inputUrl">
    <c:param name="firstName" value="${first}" />
    <c:param name="lastName" value="${second}" />
</c:url>
```

---
## Дополнительные библиотеки JSTL

- Теги форматирования
- SQL-теги
- XML-теги

Если понадобится, то нужно будет создать отдельные заметки про эти библиотеки.

---
## Кастомные теги

Кастомные теги создаются, когда не хватает функционала стандартных тегов. Для описания имени тегов, их атрибутов и выполняемых ими действий существуют [Tag Library Descriptor](tag_library_descriptor.md).

---
## К изучению

- [X] Глава 9 Head First Servlets and JSP
- [ ] Официальная спецификация: https://download.oracle.com/otndocs/jcp/jstl-1.2-mrel2-oth-JSpec/