---
title: "ORM"
tags:
  - database
draft: false
---

# ORM

**ORM** - Object-Relational Mapping

ORM - Object-Relational Mapping - объектно-реляционное отображение - технология, связывающая модель базы данных и концепции ООП.

Основные ORM:
- [Hibernate ORM](../external_lib/hibernate_orm.md) - для Java
- Django - для Python

Преимущества ORM:
- Представление модели данных в ORM независимо от СУБД
- Упрощенное моделирование БД
- ORM предоставляет больше механизмов обеспечения целостности данных
- Можно наследовать модели данных

Недостатки ORM:
- Медленнее, чем голый SQL
- Возможны проблемы с комплексными запросами
- Под каждую ORM необходимо переучиваться