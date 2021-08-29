---
title: "Представления - View"
tags:
  - database
  - sql
draft: false
---

# Представления - View

Представления служат для инкапсуляции сложного запроса по выборке данных из БД в виде отдельной таблицы.

Создав представление с помощью команды `CREATE VIEW`
```sql
CREATE VIEW complicated_Query AS
SELECT ...
    INNER JOIN ...
    RIGHT OUTER JOIN ...
    LEFT INNER JOIN ...
```

в БД создастся отдельная таблица (хотя таблицей она не будет являться, т.к. данных в ней не будет), из которой можно будет совершать запрос очень лаконичным образом:
```sql
SELECT * FROM complicated_Query;
```

При этом будет выполнен тот сложный запрос.

Удаление представления производится с помощью команды `DROP VIEW`:
```sql
DROP VIEW complicated_Query;
```

---
## К изучению

- [X] Радько Петр. Видеоурок по представлениям: https://www.youtube.com/watch?v=Dbumpd_0ojI