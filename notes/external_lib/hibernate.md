---
title: "Hibernate"
tags:
draft: false
---

**Hibernate** - это комплексный проект, включающий в том числе ORM.

[JPA](../jpa/jpa.md) был создан на основе Hibernate.

---
## Проекты Hibernate

- [Hibernate ORM](hibernate_orm.md) - основная часть Hibernate, то, с чего все начиналось. Собственно ORM.
- Hibernate EntityManager - реализация [JPA](../jpa/jpa.md). По сути является адаптером между JPA и Hibernate ORM.
- Hibernate Validator - модуль, обеспечивающий проверку классов предметной модели при записи в БД. Является единственной реализацией спецификации [Bean Validation](../java_ee/bean_validation.md).
- Hibernate Envers - модуль, ведущий журналирование и позволяющий хранить несколько версий данных, подобно тому, как это делается в Git. Библиотекой со схожим функционалом является [Javers](javers.md).
- Hibernate Search - модуль, позволяющий делать эффективный поиск по данным, хранящимся в БД.
- Hibernate OGM - предоставляет поддержку JPA для NoSQL решений.