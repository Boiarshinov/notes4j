---
title: "DDL"
tags:
  - database
  - sql
draft: false
---

# Команды DDL

**DDL** - Data Definition Language - язык описания структуры базы банных

SQL - регистронезависимый язык, но для различения имен и команд рекомендуется все командные слова писать прописными буквами. Если в названии больше двух слов, то для их разделения используется символ '_'.

- `CREATE` - создание чего-либо
- `DROP` - удаляет что-либо
- `SHOW` - показывает что-либо (таблицу, базу данных, индекс)
- `USE` - делает активной выбранную базу данных
- `ALTER TABLE` - изменяет таблицу
- `TRUNCATE` - очищает таблицу, удаляя из нее все данные
- `DESC` - выводит данные об атрибутах таблицы


## Манипуляции со схемами (базами данных)
### Создание базы данных
```sql
CREATE DATABASE [IF NOT EXISTS] db_name
```

- `CREATE` - создает
- `DATABASE (SCHEME)` - базу данных
- `IF NOT EXISTS` - (писать не обязательно) если базы данных с таким именем не существует
- `db_name` - название базы данных

```sql
CREATE DATABASE IF NOT EXISTS `db_name`;
```


### Удаление базы данных
```sql
DROP DATABASE [IF EXISTS] db_name
```

- `DROP` - удаляет
- `DATABASE` - базу данных
- `IF_EXISTS` - если она существует
- `db_name` - название базы данных

```sql
DROP DATABASE IF EXISTS `db_name`;
```

### Выбор активной базы данных
```sql
USE db_name
```

- `USE`
- `db_name`

```sql
USE `db_name`;
```


---
## Манипуляции с таблицами
### Создание таблицы
```sql
CREATE TABLE IF NOT EXISTS table_name (
    id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    column_name VARCHAR(40) NOT NULL,
    count INT DEFAULT 0
);
```

При объявлении атрибута указывается:
- `id` - имя атрибута
- `INT` - тип данных
- `[NOT NULL]` - должно быть заполнено (не может быть оставлено пустым)
- `[AUTO_INCREMENT]` - инкрементируется самостоятельно (начинается с 1)
- `[PRIMARY KEY]` - является первичным ключом
- `[DEFAULT значение]` - устанавливает значение по умолчанию, если оно не передается при инсерте

После объявления атрибутов может быть выбран первичный ключ:
- `PRIMARY KEY (id)` - устанавливает, какой атрибут будет являться первичным ключом
- `[CONSTRAINT [symbol]] FOREIGN KEY`
- `INDEX index_name (column)` - индексирует колонку, указанную в скобках


---
## Манипуляции с индексами
### Создание индекса
```sql
CREATE INDEX index_name ON table_name (column_name1, column_name2);
CREATE INDEX otdel ON shtat (dept);
```

### Демонстрация индексов таблицы
```sql
SHOW INDEX FROM shtat;
```


---
## Создание доменов (в MySQL нет доменов!)
```sql
CREATE DOMAIN domain_name [AS] data_type
    [default_definition]
    [domain_constrainr_definition_list]
```

- `default_definition` - значение по умолчанию
- `domain_constraint_definition_list` - список требований, которым должно удовлетворять значение. Проверки обозначаются словом CHECK. Проверки можно именовать с помощью ключевого слова CONSTRAINT

Пример (для PostgreSQL):
```sql
CREATE DOMAIN sale_status AS VARCHAR(45)
    DEFAULT 'new'
    CHECK (VALUE IN ('new', 'process', 'rejected'))
    CONSTRAINT sale_status_not_null CHECK (VALUE IS NOT NULL);
```


---
## Внешний ключ
```sql
[CONSTRAINT [key_name]] FOREIGN KEY [index_name] (index_col_name, ...) reference_definition
```

- Связывание - reference_definition:

`REFERENCES table_name (index_column_name, ...)` - та колонка в той таблице, от которой будет зависеть указанная ранее колонка

`[ON DELETE reference_option]` - что будет происходить со значениями в колонке при удалении внешнего ключа

`[ON UPDATE reference_option]` - что будет происходить со значениями в колонке при изменении внешнего ключа

- Варианты
    - `RESTRICT` - (рекомендуется для DELETE) запрещает изменять связанные данные
    - `CASCADE` - (рекомендуется для UPDATE) меняет каскадно, т.е. если во внешнем ключе изменилось значение, то оно меняется и в нашем атрибуте
    - `SET NULL` - устанавливаются NULL
    - `NO ACTION` - ничего не происходит (не рекомендуется к использованию)

```sql
ALTER TABLE `shtat`
ADD CONSTRAINT `depdep` FOREIGN KEY (`dept`)
REFERENCES `depart` (`id`)
ON DELETE RESTRICT
ON UPDATE CASCADE;
```

---
## Удаление данных из таблицы
Это скорее DML
```sql
TRUNCATE TABLE `shtat`;
```

## Вывод данных о таблице и ее атрибутах
```sql
DESC `shtat`;
```


---
## Изменение таблицы - ALTER TABLE

Для увеличения производительности лучше в одну команду `ALTER TABLE` напихать по максимуму операций

- ADD
    - COLUMN
        - FIRST - добавляет столбец в таблицу на первое место
        - AFTER - добавляет столбец в таблицу после указанного столбца

```sql
ALTER TABLE `shtat`
ADD COLUMN `birthday` DATE AFTER `lastname`;
```

    - [UNIQUE] INDEX

```sql
ALTER TABLE `shtat`
ADD UNIQUE INDEX `fullname` (`name`, `lastname`);
```

    - FOREIGN KEY - внешний ключ - про него см. выше

```sql
ALTER TABLE `shtat`

ADD CONSTRAINT `depdep` FOREIGN KEY (`dept`) #CONSTRAINT присваивает имя внешнему ключу

REFERENCES `depart` (`id`)
ON DELETE RESTRICT
ON UPDATE CASCADE;
```

- RENAME - переименование
    - COLUMN

```sql
ALTER TABLE `shtat`
RENAME COLUMN `salary` TO `sal`;
```

    - INDEX
- MODIFY COLUMN - изменение названия, типа данных и прочих характеристик колонки
- ALTER COLUMN - то же
- DROP
    - COLUMN - удаляет колонну (можно не писать COLUMN)
    - FOREIGN KEY

```sql
ALTER TABLE `client`
    DROP `code`,
    DROP FORIEGN KEY `fk_client_source1`,
    DROP `source_id`;
```

Чтобы удалить атрибут, являющийся внешним ключом, нужно сначала удалить ограничение внешнего ключа

- `CONVERT TO` - изменяет кодировку таблицы

```sql
ALTER TABLE `shtat` CONVERT TO CHAR SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

- `PARTITION` - уже deprecated