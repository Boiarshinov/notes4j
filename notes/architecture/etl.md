---
title: "ETL"
tags:
  - architecture
  - business_intelligence
draft: false
---

# ETL

**ETL** - Extract -> Transform -> Load.
ETL - это концепция переливки данных из одной системы в другую с преобразованием данных.
Система, из которой выгружаются данные, называется источником.
Система, в которую записываются данные, называется целевой системой.

ETL могут применяться например для переливки данных из OLTP в OLAP базы данных.

ETL противопоставляется концепция **ELT** при которой преобразования проводятся уже в целевой системе.
Такой подход позволяет не хранить данные в самой ELT системе.

Схема работы ETL:
![ETL flow](../../images/etl_flow.png)

Чаще всего концепция ETL подразумевает временное хранение части загруженных данных.
Нужно это для агрегации данных по нескольким записям.
Но не все ETL системы способны на такое.
Часть из них умеют обрабатывать записи из источника только по одной.
Например, [Kafka Connect][kafka-connect].

Существующие ETL системы:
- ???
- [Kafka Connect][kafka-connect] - фреймворк для написания конвейеров, выгружающих данные из хранилищ в Kafka, либо наоборот вычитывающих данные из Kafka и записывающих их в хранилища.

---
## К изучению
- [X] [ETL: что такое, зачем и для кого](https://chernobrovov.ru/articles/etl-chto-takoe-zachem-i-dlya-kogo.html)

[kafka-connect]: ../tools/kafka_connect.md