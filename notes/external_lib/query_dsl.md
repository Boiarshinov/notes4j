---
title: "QueryDSL"
tags:
  - java_and_db
  - external_lib
draft: true
---

**QueryDSL** - это Java-библиотека, позволяющая создавать динамические SQL-запросы к БД.

QueryDSL является аналогом JPA Criteria, но считается намного более лаконичной и понятной.

- JPAQuery -
- SQLQuery -

---
## Генерация ___ классов

QueryDSL генерирует классы, являющиеся отображениями таблиц в БД.
Генерация производится с помощью APT - annotation processing tool.
Для JPAQuery генерируются классы, начинающиеся с 'Q'.
Для SQLQuery генерируются классы, начинающиеся с 'S'.

---
## К изучению

- [ ] Официальный сайт: http://www.querydsl.com/
- [ ] Github: https://github.com/querydsl/querydsl
- [ ] Введение в QueryDSL: https://www.baeldung.com/intro-to-querydsl