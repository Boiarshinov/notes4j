---
title: "Ehcache"
tags:
  - external_lib
draft: false
---

**Ehcache** - система кэширования.

Ehcache была разработана до появления стандарта [JCache](../java/jcache.md), но начиная с версии 3.0 она поддерживает его.

---
## Уровни хранения данных

Ehcache поддерживает следующие уровни хранения данных:

- On-Heap Tier - хранит данные в куче JVM. Самый быстрый и самый ограниченный уровень хранения. При инициализации необходимо указать максимальное количество хранимых экземпляров.
- Off-Heap Tier - хранит данные в оперативной памяти. При инициализации необходимо указать объем выделяемой памяти.
- Disk Tier - использует файловую систему для хранения данных. Обладает большой емкостью, но медленной скоростью работы. Классы объектов, хранимых в кэше должны быть сериализуемы.
- Clustered Tier - кэш на удаленном сервере.

---
## Конфигурация с помощью xml

Конфигурация Ehcache может производиться с помощью xml-файла `ehcache.xml`, который помещается в директорию `resources/`.
```xml
<config
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns="http://www.ehcache.org/v3"
        xsi:schemaLocation="http://www.ehcache.org/v3
            http://www.ehcache.org/schema/ehcache-core.xsd">

    <cache alias="dogCache">
        <key-type>java.lang.String</key-type>
        <value-type>dev.boiarshinov.model.Dog</value-type>
        <resources>
            <heap unit="entries">2</heap>
            <offheap unit="MB">2</offheap>
        </resources>
    </cache>

<!--    not using, just for example-->
    <cache-template name="defaultCache">
        <key-type>java.lang.String</key-type>
        <value-type>java.lang.String</value-type>
        <heap unit="entries">200</heap>
    </cache-template>

    <cache alias="simpleCache" uses-template="defaultCache"/>
</config>
```

### Политика устаревания

Устаревание устанавливается с помощью тэга `expiry`

- `ttl` - time to live - устанавливает время жизни закешированных данных после их помещения в кэш.
- `tti` - time to idle - устанавливает время жизни закешированных данных после последнего доступа к ним.
```xml
<expiry>
    <ttl unit="minutes">10</ttl>
</expiry>
```

---
## Подключение зависимости

Для использования Ehcache в своем проекте необходимо подключить следующую зависимость:
```xml
<dependency>
    <groupId>org.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>${ehcache-version}</version>
</dependency>
```

Если при этом также планируется использование интерфейса JCache, то нужно подключить еще и
```xml
<dependency>
    <groupId>javax.cache</groupId>
    <artifactId>cache-api</artifactId>
    <version>${cache-api-version}</version>
</dependency>
```

Также Ehcache использует slf4j для логирования, так что возможно придется подключить и его.

---
## К изучению
- [ ] Официальный сайт: https://www.ehcache.org/documentation/
- [X] Отличная статья на русском: https://ru.bmstu.wiki/Ehcache