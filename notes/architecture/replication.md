---
title: "Репликация"
tags:
  - database
draft: false
---

# Репликация

**Репликация** - это хранение копии данных на нескольких нодах системы.
Нода, на которой хранятся копии, называется **репликой**.
Репликация используется для того чтобы при отказе одной из нод, данные не были потеряны.
Также репликация может использоваться для хранения данных ближе к клиентам, их запрашивающим.

Репликация используется в:
- базах данных
- файловых системах
- кэшах
- и т.д.

Репликация отличается от RAID массивов тем, что с репликами можно взаимодействовать независимо от мастер-ноды.
Также реплики могут располагаться в разных датацентрах очень далеко друг от друга.

Если на одной реплике есть данные, а на другой их нет, то возникает двоесмыслие:
- либо данные не доехали до реплики, где их нет
- либо реплика, где данные есть, не получила запрос на их удаление

Для того чтобы побороть эту проблему, для каждой записи стараются добавлять отдельное поле (колонку), в которой указывается таймстэмп последнего изменения данных.
При этом данные при удалении не удаляются полностью, а только помечаются как удаленные. 
Такие данные на сленге называются "надгробными камнями" (tombstone).
То есть для каждой записи должно быть два поля: `update_timestamp` и `is_terminated`.
Благодаря этому при расхождении данных между репликами (например после сплит-брейна) их можно будет уверенно смержить.

Так как время между нодами может [разъезжаться](distributed_systems_time.md), то вместо таймстемпов в UNIX time лучше использовать логические часы.
При использовании [часов Лэмпорта](../algorithms/lamport_clock.md) останутся только те данные, которые имеют большее значения счетчика.
При использовании [векторных часов](../algorithms/vector_clock.md)) останутся события с большим значением счетчика при наличии причинности, и все варианты события, если эти варианты являются конкурентными.


---
## К изучению
- [X] [5 лекция](https://www.youtube.com/watch?v=mBUCF1WGI_I&list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB&ab_channel=MartinKleppmann) курса по распределенным системам от Мартина Клеппманна