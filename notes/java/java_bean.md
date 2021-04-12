---
title: "JavaBean"
tags:
draft: false
---

**JavaBean** - это стандарт, который определяет каким требованием должен удовлетворять класс, чтобы считаться JavaBean.

Основная область применения JavaBean-классов - web-приложения

---

**Требования** к JavaBean классам:
- Иметь публичный конструктор без аргументов.
- Поля должны иметь публичные геттеры и сеттеры, подчиняющиеся name convention. Геттер должен возвращать объект такого же типа, который принимает сеттер.
- Класс должен быть сериализуем, т.е. реализовывать интерфейс `Serializable`. Подробно тут: [Сериализация](serialization.md).
- Должны быть переопределены методы `equals()`, `hashCode()` и `toString()`.
- При использовании в [JSP](../jsp/jsp.md): типы всех полей должны быть примитивами или строками.

---
## К изучению
- [X] http://java-online.ru/jsp-javabean.xhtml
- [X] Вики: https://ru.wikipedia.org/wiki/JavaBeans
- [X] Разница между [Spring Bean](../spring/beans.md), JavaBean и POJO: https://www.shaunabram.com/beans-vs-pojos/