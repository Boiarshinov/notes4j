---
title: "JdbcTemplate"
tags:
  - spring
  - java_and_db
draft: false
---

`JdbcTemplate` - это центральный элемент [Spring Framework JDBC](spring_framework_jdbc.md), предназначенный для составления и выполнения запросов к БД.

Он может использоваться самостоятельно, либо можно использовать более современные надстройки над ним:
- `NamedParameterJdbcTemplate` - надстройка над `JdbcTemplate`, позволяющая задавать параметры в SQL-запросах с помощью именованных переменных вместо символа `?`.
- `SimpleJdbcInsert` - элемент, позволяющий оптимизировать вставку данных в таблицы.

## `JdbcTemplate`

Для создания `JdbcTemplate` необходимо передать ему координаты БД в виде обычного `DataSource`.
Поэтому один инстанс `JdbcTemplate` могут использовать несколько DAO классов.
Это позволяет создать в приложении бин `JdbcTemplate` единожды в конфигурационном классе, а затем переиспользовать его в DAO бинах.
Проблем с многопоточностью при этом быть не должно, т.к. по документации `JdbcTemplate` является потокобезопасным.
```java
@Configuration
public class DataAccessConfig {
    /* Конфигурация DataSource */

    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }
}
```

Ничего не мешает создать по собственному инстансу `JdbcTemplate` в каждом DAO классе:
```java
@Repository
public class CatDao {
    private final JdbcTemplate jdbcTemplate;

    public CatDao(@Autowired DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }
    /* ... */
}
```


### Селекты данных

Все запросы данных выполняются с помощью методов `query()` и `queryForObject()`.

#### Запрос одного объекта

Стандартные типы данных можно запрашивать с помощью метода `queryForObject()`, указывая тип данных в виде ссылки на класс:
```java
int totalCount = jdbcTemplate.queryForObject(
    "select count(*) from cat"
    , Integer.class
);
```

Если при этом нужно вставить в запрос значение какой-либо переменной, то делается это аналогично `PreparedStatement` в JDBC с помощью вопросительного знака:
```java
int homecatCount = jdbcTemplate.queryForObject(
    "select count(*) from cat where type = ?",
    Integer.class, 
    Cat.Type.HOMECAT.name()
);
```

Если требуется собрать кастомный объект из запрошенных данных, то нужно передать в `JdbcTemplate` лямбду, которая будет преобразовывать данные из `ResultSet` в объект:
```java
Cat cat = jdbcTemplate.queryForObject(
	"select * from cat where id = ?",
	(resultSet, rowNum) ->
		Cat.builder()
			.id(resultSet.getLong("id"))
			.name(resultSet.getString("name"))
			.bornAt(resultSet.getDate("born_at").toLocalDate())
			.type(CatEntity.Type.valueOf(resultSet.getString("type")))
			.build(),
	id
);
```


#### Запрос нескольких объектов

Запрос нескольких объектов выполняется с помощью метода `query()`. При этом возвращается список объектов:
```java
List<Cat> homecats = jdbcTemplate.query(
	"select * from cat where type = ?",
	(resultSet, rowNum) ->
		Cat.builder()
			.id(resultSet.getLong("id"))
			.name(resultSet.getString("name"))
			.bornAt(resultSet.getDate("born_at").toLocalDate())
			.type(CatEntity.Type.valueOf(resultSet.getString("type")))
			.build(),
    Cat.Type.HOMECAT.name()
);
```


### Обновление данных

Чтобы вставить, обновить или удалить данные используется метод `update()`:
```java
jdbcTemplate.update("update cat set name = ? where id = ?", newName, id);
```

```java
jdbcTemplate.update("delete from cat where id = ?", id);
```


## `NamedParameterJdbcTemplate`

При передаче значений в sql-запрос с помощью `JdbcTemplate` требуется тщательно отслеживать порядок передачи аргументов в метод так, чтобы каждый аргумент соответствовал нужному `?` в запросе. 
Также бывает необходимо прокинуть одну и ту же переменную в несколько мест sql запроса. 
`NamedParameterJdbcTemplate` позволяет в sql-запросе использовать алиасы вместо вопросительных знаков и не дублировать такие переменные при составлении запроса.

Алиасы в sql-запросе устанавливаются с помощью синтаксиса `:paramName`. 
А в методы `NamedParameterJdbcTemplate` передается структура ключ-значение, в которой ключом является название алиаса, а значением - передаваемый параметр.
В качестве такой структуры ключ-значение может выступать обычная мапа, но лучше использовать одну из реализаций интерфейса `SqlParameterSource`:

```java
List<Cat> sleepingAtTimeHomecats = namedParameterJdbcTemplate.query(
    "select * from cat"
    + " where type = :type"
    + "   and sleep_from > :time"
    + "   and sleep_to < :time",
    new MapSqlParameterSource()
        .addValue("type", Cat.Type.HOMECAT)
        .addValue("time", dateTime),
    catRowMapper() //лямбда с маппингом, вынесенная в отдельный метод
);
```


## Запись с помощью `SimpleJdbcInsert`

`SimpleJdbcInsert` позволяет упростить добавление записей в таблицу по сравнению с `JdbcTemplate`. Делается это за счет использования метаданных о таблице, которые `SimpleJdbcInsert` вытягивает из СУБД.

### Создание экземпляра

Создание экземпляра `SimpleJdbcInsert` обычно производится в конструкторе DAO класса.
```java
public class CatDao {
    private final SimpleJdbcInsert catInsert;

    public CatDao(DataSource dataSource) {
        this.catInsert = new SimpleJdbcInsert(dataSource)
            .withTableName("cat");
    }
}
```

При использовании сервера СУБД с несколькими схемами нужно указать еще и имя схемы. 
В некоторых СУБД при этом может вылетать ошибка вытягивания метаданных таблицы.
В таком случае нужно указать названия всех используемых колонок и отключить вытягивание метаданных:
```java
this.catInsert = new SimpleJdbcInsert(dataSource)
    .withTableName("cat")
    .withSchemaName("first_schema")
    .usingColumns("name", "age", "type", "born");
this.catInsert.withoutTableColumnMetaDataAccess();
```

### Добавление записей

Для добавления записи в таблицу нужно передать в `SimpleJdbcInsert` набор пар ключ-значение. 
Сделать это можно с помощью обычной мапы, в которой ключом является название колонки:
```java
catInsert.execute(
    Map.of(
        "name", cat.getName(),
        "age", cat.getAge(),
        "type", cat.getType().name(),
        "born", cat.getBorn()
    )
);
```

Но лучше делать это с помощью специальной структуры `MapSqlParameterSource`:
```java
catInsert.execute(
    new MapSqlParameterSource()
        .addValue("name", cat.getName())
        .addValue("age", cat.getAge())
        .addValue("type", cat.getType().name())
        .addValue("born", cat.getBorn())
);
```

Зачастую первичный ключ записи генерируется СУБД, и при записи данных мы хотим узнать какой идентификатор присвоен записи.
Для этого нужно воспользоваться специальным методом `executeAndReturnKey()`, который вернет сгенерированное значение в формате `Number`.
```java
long id = catInsert.executeAndReturnKey(
    new MapSqlParameterSource()
        .addValue("name", cat.getName())
        .addValue("age", cat.getAge())
        .addValue("type", cat.getType().name())
        .addValue("born", cat.getBorn())
).longValue();
cat.setId(id);
```

Конфигурация `SimpleJdbcInsert` при этом обязательно должна содержать указание того, какая колонка является первичным ключом с автогенерируемым значением:
```java
this.catInsert = new SimpleJdbcInsert(dataSource)
    .usingGeneratedKeyColumns("id")
    /* дальнейшая конфигурация */
```

---
## К изучению

- [X] [Официальная документация](https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#jdbc) 