---
title: "Типы данных"
tags:
  - database
  - sql
draft: false
---

# Типы данных

## Числовые типы данных

Зеленым выделены общие для всех СУБД типы. Остальные типы актуальны только для MySQL. Желательно выделить их в отдельную категорию

- Целые: форма записи - INT[(M)] [UNSIGNED] [ZEROFILL]
    - `tinyint` - 1 байт
    - `smallint` - 2 байта
    - `MEDIUMINT` - 3 байта
    - `int` (синоним - `INTEGER`) - 4 байта
    - `bigint` - 8 байт
- Вещественные: форма записи - `DOUBLE[(M, D)] [UNSIGNED] [ZEROFILL]`
    - `float(точность)` - устанавливает количество знаков перед и после запятой
```sql
FLOAT(3) # 3 знака после запятой
FLOAT(3, 4) # три знака перед запятой и 4 после
```

    - `real` - тип float с предустановленной точностью. Занимает 34 бита.
    - `double precision` - real с двойной точностью. Занимает 64 бита.
    - `DECIMAL` - число с более чем 500 символами, хранится как строка
    - `DEC` - неведомая хрень
    - `NUMERIC` - неведомая хрень

Дополнительные модификаторы:

- `(M)` - ограничивает количество символов, которые могут быть указаны. Не экономит память!
- `(M, D)` - `M` - количество знаков, `D` - количество разрядов за десятичной точкой
- `UNSIGNED` - беззнаковый формат. По умолчанию он не указан
- `ZEROFILL` - заполняет нулями неизрасходованные позиции в числе. Позволяет восстановить большое количество данных при сбое, из-за отсутствия фрагментации данных

---
## Временные типы

### Стандартные типы

- `date` - ГГГГ-ММ-ДД
- `timestamp[(M)]` - ГГГГ-ММ-ДД ЧЧ:ММ:СС - не хранит временную метку часового пояса. Далее актуально для MySQL: //Противоречит документации MySQL. Число в скобках означает количество знаков после запятой для секунд
    - M = 6 - ГГГГММ
    - М = 8 - ГГГГММДД
    - М = 10 - ГГГГММДДЧЧ
    - М = 12 - ГГГГММДДЧЧММ
    - М = 14 - ГГГГММДДЧЧММСС

`timestamp` хорош тем, что может при создании новых строк в таблице или при их апдейте автоматически принимать текущее значение времени:
```sql
CREATE TABLE table_name (
    ...
    time_of_last_update TIMESTAMP ON UPDATE CURRENT_TIMESTAMP); #Значение будет устанавливаться автоматически при апдейте
```

В MySQL `timestamp` может принимать значения только от 1970 г. до 2038 г., что делает его неподходящим кандидатом для хранения данных о датах рождения, астрономических событиях и проч.

- `time` - ЧЧ:ММ:СС - время дня без метки часового пояса

### Дополнительные типы MySQL

- `datetime` - ГГГГ-ММ-ДД ЧЧ:ММ:СС - хранит временную метку часового пояса, что выгодно отличает его от timestamp.
- `year[(2 | 4)]`
    - 2-значный формат - от 1970 до 2036
    - 4-значный формат - сколько хочешь

### Дополнительные типы PostgreSQL

Дополнительные типы PostgreSQL:

- `timestamp with time zone (timestamptz)` - `timestamp` с меткой временного пояса. Занимает 8 байт.
- `time with time zone` - время дня с часовым поясом. Занимает 12 байт.
- `interval`

---
## Строки

В стандарте SQL определены следующие символьные форматы данных:
- `char` - один символ
- `character varying`
- `character`
- `varchar`

???
- `[NATIONAL] CHAR(M) [BINARY]` - хранит 256 символов, неиспользуемые заполняет пробелами
- `CHAR` - хранит 1 символ
- `[NATIONAL] VARCHAR(M) [BINARY]` - строка переменной длины от 0 до 255 символов
- `TINYBLOB` (синоним - `TINYTEXT`) - хранит 2^8 символов
- `BLOB` (синоним - `TEXT`) - хранит 2^16 символов
- `MEDIUMBLOB` (синоним - `MEDIUMTEXT`) - хранит 2^24 символов
- `LONGBLOB` (синоним - `LONGTEXT`) - хранит 2^32 символов
- `LONGTEXT`

Модификаторы:
- `NATIONAL` -
- `BINARY` - текст становится чувствительным к регистру (по умолчанию нечувствителен)

---
## Логические типы

В стандарте SQL определен только один логический тип данных:

- `boolean` - истина или ложь

### MySQL

В MySQL не существует типа `boolean`. При попытке создания колонки такого типа `boolean` будет преобразован в `tinyint(1)`.

### PostgreSQL

В PostgreSQL значение boolean занимает 1 байт.

---
## Данные

Обычно данные предпочитают хранить не в БД, а в файловой системе, при этом в БД указывать только путь до файла. Но иногда имеет смысл запихнуть в БД бинарник, в таком случае можно использовать один из следующих форматов:

- `BLOB` - binary large object
- `CLOB` - character large object

---
## Перечисления

Перечисления используются в тех случаях, когда для поля существует строго определенное количество известных состояний. Перечисления позволяют обеспечить типобезопасность записываемых данных, уберегая от опечаток.

### MySQL

В MySQL перечисление объявляется как тип колонки при создании таблицы, там же объявляются допустимые значения.
```sql
create table <table_name> (<column_name> ENUM(<enum_val1>, <enum_val2>, ...));
```

### PostgreSQL

В PostgreSQL объявление перечисления производится с помощью команды
```sql
create type <type_name> as enum (<value1>, <value2>, ...);
```
Все перечисления хранятся в схеме в директории `object types`.

---
## К изучению

- [X] Числа в SQL: https://learnsql.com/blog/understanding-numerical-data-types-sql/
- [ ] Типы данных в MySQL: https://dev.mysql.com/doc/refman/8.0/en/data-types.html
- [ ] Типы данных в PostgreSQL: https://postgrespro.ru/docs/postgresql/11/datatype