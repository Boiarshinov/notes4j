---
title: "PostgreSQL. Мониторинг"
tags:
  - database
  - sql
  - dbms
draft: false
---

# PostgreSQL. Мониторинг

PostgreSQL предоставляет ряд системных вьюх / таблиц, которые могут использоваться для мониторинга приложения.

## Доступность базы

### Время отклика

из таблицы `pg_stat_statements` сделать выборку
```sql
sum(total_time) / sum(calls)
```

### Количество ошибок

из таблицы `pg_stat_database` сделать выборку
```sql
sum(xact_rollback)
```


## Объем базы данных

Для того чтобы узнать размер всей базы данных, нужно выполнить запрос
```sql
select pg_size_pretty(pg_database_size('<db_name>'));
```

Размер одной таблицы вместе с индексами можно узнать с помощью запроса
```sql
select pg_size_pretty(pg_total_relation_size('<table_name>'))
```

## Статистика использования

Статистика использования индексов
```sql
select
      relname as table,
      indexrelname as index,
      pg_size_pretty(pg_relation_size(i.indexrelid)) as index_size,
      idx_scan as index_scans
from pg_stat_user_indexes ui
join pg_index i on ui.indexrelid = i.indexrelid
where not indisunique and idx_scan < 50
  and pg_relation_size(relid) > 5 * 819
order by pg_relation_size(i.indexrelid) / nullif(idx_scan, 0) desc nulls first;
```

---
## К изучению

- [ ] Размер базы и ее частей: https://ru.stackoverflow.com/questions/321
- [ ] Основы мониторинга: https://www.youtube.com/watch?v=Hbi2AFhd4nY