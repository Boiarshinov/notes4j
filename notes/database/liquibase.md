---
title: "Liquibase"
tags:
  - database
draft: false
---

# Liquibase

**Liquibase** - это инструмент для описания и автоматического раскатывания / откатывания миграций на базу данных.

Аналогом Liquibase является Flyway.

Liquibase бесплатен, но у него есть платная PRO-версия с дополнительными фичами: работа с хранимыми процедурами и триггерами, откат определенных changeset'ов и т.д.

Liquibase поддерживает работу со всеми основными СУБД:
- PostgreSQL
- MySQL
- Oracle
- прочие

Liquibase позволяет описывать миграции в одном из следующих форматов:
- XML
- JSON
- YAML
- SQL

### Терминология

*Changelog* - текстовый файл, содержащий в себе описание изменений в схеме или наполнении базы данных. Каждый changelog содержит в себе один или более changeset. Changelog'и должны храниться в системе контроля версий.

*Changeset* - единица изменения БД. Каждый changeset накатывается отдельно. Changeset содержит в себе один или более change type. Хорошей практикой является выделение отдельного changeset для каждого change type.

*Change type* - атомарное изменение в схеме БД. Например, добавление новой колонки в таблицу или добавление индекса.

*Tracking table* - специальные таблицы, генерируемые и заполняемые liquibase, для того чтобы отслеживать изменения в схеме БД.

### Отслеживание изменений

Liquibase отслеживает историю изменений с помощью двух системных таблиц:

- `databasechangelog` - содержит в себе информацию о каждом changeset'е: идентификатор, ссылка на файл с миграцией, хэш-сумма файла с миграцией, автор, дата накатывания и проч.
- `databasechangeloglock` - содержит сведения о блокировке таблицы `databasechangelog`. Таким образом можно быть уверенным в том, что только один инстанс liquibase может накатывать миграции на базу в конкретный момент времени.

Liquibase при накатке миграций смотрит в таблицу `databasechangelog`, чтобы определить какие миграции уже применены к БД. Чтобы миграция выполнялась при каждом запуске liquibase, нужно прописать ей `runAlways = true`, но это достаточно редкий кейс.

Если миграция изменилась, то liquibase откажется ее накатывать и упадет с ошибкой, т.к. у нее поменяется хэш-сумма. Для того чтобы разрешить накатывать миграции при их изменении, нужно прописать `runOnChange = true`, тогда liquibase будет накатывать миграцию при изменениях хэш-суммы.

---
## Точки входа

Liquibase имеет несколько точек входа: CLI и Maven-плагин

### CLI - Command Line Interface

Для использования CLI необходимо установить Liquibase, скачав дистрибутив с официального сайта.

После этого в консоли можно будет выполнять следующие команды:

- `update` - накатывает изменения
```sh
$ liquibase --changeLogFile=my_changelog.xml update
```
- `updateCount <num>` - накатывает строго указанное количество новых changeset'ов.
- `rollback` - откатывает изменения. Liquibase самостоятельно генерирует rollback миграции. В тех случаях, когда нужно описать еще какие-либо дополнительные изменения (чаще всего в данных), нужно прописать обратную миграцию в теге rollback
    - `rollbackDate` - откатывает изменения к определенному времени. Дата/время указывается в  формате yyyy-MM-dd'T'HH:mm:ss
    - `rollbackCount <num>` - откатывает заданное количество changeset'ов
    - `rollbackTag <tag>` - откатывает изменения до определенного тэга
- `generateChangeLog` - создает единый changelog по текущему состоянию БД. Может использоваться при внедрении Liquibase в существующий проект.
- `updateSql` - выводит sql, который liquibase собирается накатить на базу при следующем апдейте.
- `history` - история всех миграций, накаченных на БД
- `status` - список ненакаченных миграций
- `snapshot` - снимок текущего состояния БД. Позволяет делать сохранять текущую схему БД в виде yaml-файла.
- `diff` - сравнение схем двух БД. Можно сравнивать текущую схему БД с ранее сделанным снэпшотом. Сравнение можно проводить по фильтрам change type'ов.
```sh
$ liquibase --outputFile=diff.txt --diffTypes=tables, indexes, views diff
```
- `diffChangeLog` - генерация changelog'а по отличиям в схеме двух БД. Используется для синхронизации схем нескольких БД.
- много других

### Maven плагин

Для Java-проектов можно использовать [Maven-плагин](/notes/build/maven.md), который подключается, как обычно, в pom.xml.
```xml
            <plugin>
                <groupId>org.liquibase</groupId>
                <artifactId>liquibase-maven-plugin</artifactId>
                <version>4.0.0</version>
                <configuration>
                    <propertyFile>liquibase.properties</propertyFile>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>org.postgresql</groupId>
                        <artifactId>postgresql</artifactId>
                        <version>42.2.18</version>
                    </dependency>
                </dependencies>
            </plugin>
```
Maven плагин предоставляет те же самые команды, что и CLI.

---
## Настройки Liquibase

Для настройки Liquibase для каждого конкретного проекта используется файл `liquibase.properties`.

В файле пропертей необходимо указать:
- changeLogFile - путь до главного changelog'а
- driver - JDBC-драйвер используемой СУБД
- url - путь до БД
- username - логин
- password - пароль

Существуют и другие параметры, которые могут быть определены в данном файле. Например координаты до БД, развернутой на каком-либо контуре для выполнения команды `diff` и определения различий в схемах локальной и боевой БД, такие координаты начинаются с суффикса `reference`.

Пример файла:
```properties
changeLogFile = db/changelog/db.changelog.sql
url = jdbc:postgresql://localhost:5432/postgres
username = postgres
password = postgres
driver = org.postgresql.Driver
```

---
## Workflow

1. Написание changelog'а
2. Проверка генерируемого sql с помощью команды `updateSql`.
3. Залить changelog в свою VCS.
4. Накатить изменения на базу с помощью команды `update`.
5. Проверить, что изменения были накачены на БД с помощью команды `history` или команды `status`

---
## SQL-changelog

Liquibase позволяет описывать миграции с помощью sql. Это наиболее мощный способ и одновременно наименее гибкий, т.к. он позволяет описывать миграции с учетом фичей, индивидуальных для используемой СУБД, но при этом теряется возможность перенести миграции на другую СУБД.

Каждый sql-changelog должен начинаться комментарием
```sql
-- liquibase formatted sql
```
Для того чтобы разбить sql-файл на changeset используются комментарии:
```sql
--changeset author:id
create table user (
    id int primary key,
    name varchar(255) not null
);
```

Автоматический откат миграций, записанных в SQL, не работает, поэтому их необходимо описывать явно.

---
## XML-changelog

В отличие от способа описания миграций с помощью sql, xml-changelog (а также yaml и json) является независимым от используемой СУБД.

XML-файлы с миграциями содержат в себе рутовый тег changelog'а, в котором описываются changeset'ы.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">
    <changeSet id="1" author="boiarshinov">
        <!--        -->
    </changeSet>
</databaseChangeLog>
```

---
## Работа с changelog'ами

Для того чтобы упростить себе работу с changelog'ом стоит разбить его на несколько отдельных файлов. Обычно главный changelog называется master и в него импортируются остальные changelog'и.

![image.png](/images/liquibase_changelogs_structure.png)

Имеет смысл выделить отдельный мастер changelog для создания схемы и отдельный мастер changelog для заполнения справочных таблиц.

Главный changelog в таком случае подтягивает с помощью тэга `include` изменения из мастер changelog'ов:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">
    <include file="db.changelog-schema.xml" relativeToChangelogFile="true" />
    <include file="db.changelog-data.xml" relativeToChangelogFile="true" />
</databaseChangeLog>
```

А мастер changelog'и для схемы и для справочников подтягивают все changelog'и из отдельных директорий с помощью тега `includeAll` следующим образом:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">
    <includeAll path="data" relativeToChangelogFile="true"/>
</databaseChangeLog>
```

changelog'и при этом будут считываться в алфавитном порядке, поэтому рекомендуется иметь стратегию их наименования. Стратегия может быть основана на числовом или временном префиксе: `2020-10-24_add_users_table.xml`.

---
## Применение в Java-приложениях

Для использования Liquibase в Java-приложениях необходимо подключить к своему проекту зависимость
```xml
<dependency>
    <groupId>org.liquibase</groupId>
    <artifactId>liquibase-core</artifactId>
</dependency>
```

### Интеграция со Spring Boot

При использовании Spring Boot необязательно создавать файл с настройками liquibase, эти настройки могут храниться в application.properties / yaml. При этом liquibase будет использовать ту БД, которая указана в настройках datasource:
```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/postgres?currentSchema=nonograms
    username: postgres
    password: postgres
    driver-class-name: org.postgresql.Driver
  liquibase:
    change-log: classpath:db/changelog/db.changelog-master.xml
```

Spring Boot будет самостоятельно накатывать миграции на БД при запуске приложения.

[Spring Boot Actuator](/notes/spring/actuator.md) позволяет отслеживать состояние схемы БД благодаря интеграции с Liquibase. Накаченные changeset'ы можно посмотреть по умолчанию по URL `<host>/actuator/liquibase`.

---
## К изучению

- [X] Курс от создателей Liquibase: https://learn.liquibase.com/catalog/info/id:125,cms_featured_course:1
- [X] Советы по работе с Liquibase: https://habr.com/ru/company/epam_systems/blog/489198/
- [X] Liquibase в Spring Boot приложении. Часть 1: https://habr.com/ru/post/460377/
- [X] Liquibase в Spring Boot приложении. Часть 2: https://habr.com/ru/post/460907/
- [ ] Про откат миграций: https://www.baeldung.com/liquibase-rollback