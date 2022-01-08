---
title: Выборка данных - `SELECT`
tags:
  - database
  - sql
draft: false
---

# Выборка данных - `SELECT`

Выборка данных относится к [DML](dml.md) синтаксису SQL.

В простейшем виде запрос на выборку данных из таблицы выглядит следующим образом:
```sql
SELECT atribute1, atribute2 FROM [database1.]table1;
```

Части `SELECT` и их обязательность:
```sql
SELECT [DISTINCT | ALL] {*
    | [<column_name> [[AS] <alias>]] [,...]}
FROM <table_name> [[AS] <alias>] [,...]
[WHERE <predicate>]
[[GROUP BY <column_names>]
    [HAVING <group_predicate>]]
[ORDER BY <column_names>]
```
Здесь в квадратных скобках приведены необязательные части.

Можно объединять данные, полученные из разных атрибутов, с помощью `CONCAT`, и менять название столбцов, выводимое на экран с помощью псевдонимов (алиасов) `AS`:
```sql
SELECT CONCAT(first_name, ' ', lastname) AS ФИО FROM shtat;
```

Из результирующей выборки можно убрать дублирующиеся кортежи с помощью слова `DISTINCT`
```sql
SELECT DISTINCT first_name, salary FROM shtat;
```

Чтобы не перечислять все атрибуты одной таблицы в `SELECT` при выборке из нескольких таблиц можно указать их с помощью имени таблицы и звездочки:
```sql
SELECT table1.*, table2.created_at
FROM table1
JOIN table2 ON ...;
```

Можно изменять извлекаемые значения согласно условию.
Делается это с помощью конструкции `CASE WHEN condition THEN value ELSE default_value END`
В MySQL дополнительно есть еще тернарная функция `IF(condition, value, default_value)`.
```sql
/* postgres and mysql */
SELECT id, good_name, CASE 
        WHEN good_type = 'book' THEN price * 1.2
        WHEN good_type = 'meal' THEN price * 1.1
        ELSE price END
    AS corrected_price FROM good;

/* mysql */
SELECT id, good_name, IF(good_type = 'book', price * 1.2, IF(good_type = 'meal', price * 1.1, price)) AS corrected_price FROM good;
```


## Фильтрация
При выборке из таблицы можно отсечь те записи, которые не удовлетворяют какому-либо условию.
Делается это с помощью оператора `WHERE`.
Условие представляет собой предикат составленный с помощью обычных операций сравнения.
Для сравнения с `NULL` используется специальный синтаксис:
- `IS NULL`
- `IS NOT NULL`

Условия можно комбинировать с помощью слов `OR`, `AND` и скобок:
```sql
SELECT DISTINCT name, salary FROM shtat
WHERE (name = 'Artem' OR name = 'Lina') AND salary > 2000;
```

Подробно про составление предикатов можно почитать в отдельной [заметке](predicates.md).


## Сортировка
Для сортировки по какому-либо признаку используется оператор `ORDER BY`.
После ключевого слова указывается атрибут, по которому проводится сортировка. 
Можно указать несколько атрибутов, тогда после сортировки по первому кортежи с одинаковыми значениями этого атрибута, будут отсортированы по следующему.
Для того чтобы задать направление сортировки: по возрастанию или по убыванию, используются ключевые слова:
- `ASC` (по умолчанию) - в порядке возрастания (NULL раньше всех)
- `DESC` - в порядке убывания

```sql
SELECT * FROM table
WHERE price > 500
ORDER BY price DESC, speed; /* ORDER BY 2 DESC, 3 - можно указывать не название, а номер атрибута, но так лучше не делать */
```

При сортировке по времени можно использовать функции работы со временем:
```sql
SELECT name, birthday FROM employee
ORDER BY MONTH(birthday), DAY(birthday);
```


## Группировка
Выбирая данные их можно сгруппировать.
Например, можно сделать группировку по уникальным значениям какой-либо колонки.
Для этого используется оператор `GROUP BY`.
```sql
SELECT user_type FROM user
GROUP BY user_type;
/* Аналогом такого запроса будет являться: */
SELECT DISTINCT user_type FROM user;
```

Помимо этого группировка позволяет объединять данные из других столбцов с помощью [агрегатных функций](aggregate_functions.md).
```sql
SELECT atribute_1, AGR_FUNC(atribute_2) FROM table
GROUP BY atribute_1
```

Пример:
```sql
SELECT category, SUM(spend) FROM expenses
GROUP BY category;
```

Группировка может быть проведена и по нескольким столбцам одновременно:
```sql
SELECT category, author, SUM(spend) FROM expenses
GROUP BY category, author;
```

Также группировка может проводиться по вычисляемым выражениям, вместо колонок:
```sql
SELECT * FROM order
GROUP BY SQRT(price), DATEDIFF(updated_at, created_at);
```

`HAVING` - позволяет задать условия фильтрации для групп.
После ключевого слова передается предикат, так же как после `WHERE`.
```sql
SELECT category, SUM(spend) FROM expenses
GROUP BY category
HAVING AVG(spend) > 5000;
```


## Прочие функции

- `ROLLUP`
- `LIMIT` -
    - `LIMIT count` - выводит только переданное количество кортежей
    - `LIMIT from, count` - выводит только переданное количество кортежей, начиная с порядкового номера, переданного первым аргументом
- `JOIN`
- `UNION`


## Порядок выполнения запроса
В разных СУБД интерпретатор по-разному считывает и выполняет запрос.
Из-за этого алиасы, объявленные после `SELECT` не опознаются в разделе `WHERE`.
```
MySQL:
FROM -> WHERE -> SELECT -> GROUP BY -> HAVING -> ORDER BY -> LIMIT

PostgreSQL:
FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> DISTINCT -> ORDER BY -> LIMIT
```


## Получение boolean значения

Зачастую необходимо получить информацию есть ли в таблице хоть одна запись, удовлетворяющая условию.

Для этого можно воспользоваться ключевым словом `EXISTS`:
```sql
SELECT EXISTS (SELECT * FROM my_table WHERE id > 10);
```

Такой запрос вернет boolean значение в зависимости от того нашлась ли хоть одна запись, удовлетворяющая условию.


---
## Подзапросы

Вложенный запрос - это `SELECT`, записанный внутри другого `SELECT`.
Применять вложенные запросы приходится достаточно часто, когда необходимо при выборке делать сравнения с результатами другой выборки.

Вложенный `SELECT` может быть записан внутри предиката `WHERE` или `HAVING`, или даже может использоваться для вычисления еще одного столбца результирующей выборки.
Записывается подзапрос в скобках:
```sql
SELECT author, title, price,
       amount / (SELECT SUM(amount) FROM book) AS partitioning
FROM book
WHERE price < (SELECT AVG(price) FROM book);
```

Если в результате выполнения подзапроса получается единственный результат, то его можно использовать для сравнения с каким-либо значением.
Если же подзапрос может вернуть несколько значений, то для сравнения с результатом нужно использовать ключевое слово `IN`
```sql
SELECT * FROM book
WHERE genre IN (SELECT DISTINCT genre FROM book WHERE price < 200);
```

### `ALL` и `ANY`
Также для образования предикатов могут использоваться ключевые слова `ALL` и `ANY`.

`ANY` означает, что сравниваемое значение должно удовлетворять условию для **любого** из значений, вернувшихся из подзапроса:
```sql
SELECT * FROM book
WHERE genre = ANY (SELECT DISTINCT genre FROM book WHERE price < 200);
/* Получился аналог запроса с IN */
```

`ANY` означает, что сравниваемое значение должно удовлетворять условию для **всех** значений, вернувшихся из подзапроса:
```sql
SELECT * FROM book
WHERE amount < ALL (SELECT amount FROM book WHERE price < 500);
/* Меньше каждого из вернувшихся значений. Тот же запрос можно написать и иначе */
```




---
## К изучению 
- [X] Курс [Интерактивный тренажер по SQL](https://stepik.org/course/63054/syllabus)