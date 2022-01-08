---
title: "DML"
tags:
  - database
  - sql
draft: false
---

# Команды DML

**DML** - data manipulating language - язык для манипуляциями над данными

Команды DML:

- `INSERT INTO` - вставляет данные в таблицу
- `SELECT` - выбирает данные и отображает их
- `DELETE` - удаляет данные из таблицы
- `UPDATE` - обновляет данные в таблице

---
## Вставка значений - INSERT
```sql
INSERT INTO table_name (column_1, column_2, ...) 
VALUES (value_1, value_2, ...),
       (value_1, value_2, ...),
       (value_1, value_2, ...);
```

Автоинкрементируемые атрибуты можно не прописывать и значения им не присваивать - это будет сделано автоматически!

Если заранее известно, что какой-то атрибут будет `NULL`, то можно его не прописывать, `NULL` ему будет присвоен автоматически.

Существует еще одна форма записи:
```sql
INSERT INTO table_name
VALUES (value_1, value_2, ...); /* Работает, только если количество значений равно количеству атрибутов
и переданные типы данных подходят под типы данных атрибутов в том соответствующем порядке */
```

Данные можно переливать из одной таблицы в другую с помощью вложенных запросов на выборку:
```sql
INSERT INTO table_to (column_1, column_2, ...) 
SELECT (column_from_1, column_from_2, ...)
FROM table_from;
```


---
## Выборка SELECT
Для простейшей выборки данных используется следующий синтаксис:
```sql
SELECT atribute1, atribute2 FROM [database1.]table1;
```

Полный синтаксис составления запросов на выборку настолько разнообразен, что ему посвящена [отдельная заметка](select.md).


---
## Обновление UPDATE

Можно обновлять данные
```sql
UPDATE table_1 SET atribute_1 = value_1, atribute_2 = value_2 
WHERE atribute_1 > value_3;
```

- `CASE`
```sql
UPDATE table_1 SET atribute_1 = CASE
WHEN atribute_2 > 1000 THEN value_1 /* # Последовательно проверяет выполнение условий и присваивает необходимое значение */
WHEN atribute_2 > 100 THEN value_2
WHEN atribute_2 > 10 THEN value_3
ELSE value_4;
```

После ключевого слова `UPDATE` можно указать несколько таблиц.
Таким образом можно обращаться к значениям в столбцах нескольких таблиц, при этом обновлять данные необязательно во всех из них.
```sql
UPDATE book, supply
SET book.amount = book.amount + supply.amount, book.price = (book.price + supply.price) / 2
WHERE book.author = supply.author AND book.title = supply.title;
```


---
## Удаление DELETE

Можно удалять данные из таблиц
```sql
DELETE FROM table_1 WHERE atribute_1 > value_1 OR atribute_2 < value_2;
```

В примере выше из таблицы удаляются все кортежи, для которых выполняются заданные условия.

Для того чтобы удалить из таблицы все данные, нужно не указывать предикатов
```sql
DELETE FROM table_1
```


---
## К изучению 
- [X] Курс [Интерактивный тренажер по SQL](https://stepik.org/course/63054/syllabus)
