---
title: "Java EE - Enterprise Edition"
tags:
  - java_ee
draft: false
---

**Java EE** - представляет собой набор библиотек, которые используются для создания Enterprise-приложений.

---
## История развития
Java EE появилась в 1999 году.

В 2018 Oracle передала Java EE в open-source и с тех пор она называется Jakarta EE.

Нововведения в различных версиях Java EE описаны тут: [Нововведения в версиях Java](../java/java_versions.md)

---
## Основные API

- Java [Servlets](../servlets/servlet.md) - классы, обрабатывающие HTTP-запросы от клиентов
- [JSP](../jsp/jsp.md) - Java Server Pages - html-разметка с java-кодом
- EJB - Enterprise JavaBeans - для классов с бизнес-логикой
- [CDI](cdi.md) - Contexts and Dependency Injection - внедрение зависимостей
- JSON-P - JSON Processing - позволяет работать со строками JSON
- JSON-B - JSON Binding - функционал для сериализации и десериализации объектов JSON в Java
- WebSocket -
- JMS - Java Message Service - для пересылки сообщениями между клиентами
- Security API -
- JAX-RS - Java API for RESTful Web Services - для построения RESTful приложений
- JSF - Java Server Faces - для создания пользовательского интерфейса на стороне сервера

---
## Java EE и IntelliJ IDEA

Стандартная версия IDEA Community ed. не поддерживает спецификации java EE из коробки, но их можно подгрузить с помощью Maven.

Либо можно установить IDEA Ultimate ed., в которой весь Java EE стек есть из коробки. Но она распространяется по платной подписке.

---
## К изучению

- [X] [Кратко про Java EE](https://metanit.com/java/javaee/1.1.php)