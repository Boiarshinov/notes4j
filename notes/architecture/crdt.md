---
title: "CRDT"
tags:
  - distributed_system
draft: false
---

# CRDT

**CRDT** - Conflict free replicated data types - специальные форматы данных, которые используются в распределенных системах, для того чтобы обеспечить возможность совместного редактирования документов (Google Docs, Miro, Confluence) и безошибочную синхронизацию данных между устройствами (Google Calendar).

CRDT бывают двух видов:
- Operational-based - основанные на операции
- State-based - основанные на состоянии системы. Требуют 

State-based толерантны к потере сообщений, но требуют пересылки всего состояния системы между нодами, что очень грузит сеть.
Operational-based имеют маленькие сообщения, а значит их легче использовать при большом количестве нод.

Простейшая CRDT структура - это мапа (key-value).


## Совместное редактирование текста
Совместное редактирование текста - непростая задача.
Если не использовать CRDT, то нужно передавать информацию об операциях, используя total order броадкаст.
Помимо этого на каждой ноде должен быть реализован алгоритм, который сможет мержить события, произошедшие конкурентно.

Вместо этого можно использовать CRDT структуру данных.
Тогда требования к броадкастингу упадут и можно будет использовать causal broadcast.

Например, строка может быть представлена не массивом символов, а множеством (не массивом) кортежей из трех значений: 
- вес символа
- идентификатор ноды
- значение символа


---
## К изучению
- [X] [8 лекция](https://www.youtube.com/watch?v=OqqliwwG0SM&list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB&index=27&ab_channel=MartinKleppmann) курса по распределенным системам от Мартина Клеппманна
- [ ] [Статья на Хабре](https://habr.com/ru/post/418897/)