---
title: "Hazelcast"
tags:
  - database
  - java_and_db
draft: false
---

Hazelcast - это серия продуктов компании Hazelcast.
Основными продуктами компании являются:

- Hazelcast IMDG - одна из реализаций [IMDG](../database/imdg.md).
- Hazelcast Jet - сервис для распределенных вычислений

---
## IMDG

Hazelcast IMDG - это распределенное хранилище (в том числе key-value).

Обычно Hazelcast IMDG используется для быстрого совместного доступа к данным из нескольких инстансов одного приложения.

Hazelcast IMDG может быть развернут несколькими способами:

- Embedded - когда hazelcast поднимается внутри приложения. Обычно используется для тестирования и в микросервисной архитектуре
- Client/Server - инстансы hazelcast поднимаются независимо от основного приложения. Такая архитектура дает дополнительные затраты на доставку сообщений, но позволяет масштабировать хранилище независимо от приложения
- Cloud - то же самое, что Client/Server, но не нужно иметь свое железо

Hazelcast IMDG реализует стандарт [JCache](../java/jcache.md) и может использоваться в качестве распределенного кэша через его API.

### REST интерфейс

Hazelcast предоставляет REST интерфейс для получения информации о Hazelcast. По умолчанию он выключен. Для того чтобы включить, необходимо в `hazelcast.xml` добавить пропертю:
```xml
<property name="hazelcast.rest.enabled">true</property>
```

Список эндпойнтов:

- `GET <host:port>/hazelcast/rest/cluster` - информация о нодах в кластере
- `GET <host:port>/hazelcast/rest/maps/<map>/<key>` - чтение из мапы по ключу
- `POST <host:port>/hazelcast/rest/maps/<map>/<key>` - запись в мапу ключ-значение
- `POST <host:port>/hazelcast/rest/management/cluster/state` - запрос состояния кластера. В теле запроса нужно передавать `<group>&<password>` в виде raw data.
- `POST <host:port>/hazelcast/rest/management/cluster/changeState` - перевод кластера в другое состояние. В теле запроса нужно передавать `<group>&<password>&<state>` в виде raw data.

Отдельно стоит healthcheck эндпойнт, для включения которого необходимо прописать пропертю:
```xml
<property name="hazelcast.http.healthcheck.enabled">true</property>
```

- `GET <host:port>/hazelcast/health` - состояние кластера и данной ноды

---
## К изучению

- [X] Официальный сайт: https://hazelcast.org/

### IMDG

- [X] Документация: https://docs.hazelcast.com/imdg/latest/index.html
- [ ] Курс по основам от самого хазеля: https://training.hazelcast.com/introduction-to-hazelcast
- [X] Репозиторий Hazelcast IMDG: https://github.com/hazelcast/hazelcast
- [X] Доклад Гамова на JPoint по Hazelcast в качестве кэша: https://www.youtube.com/watch?v=mU893v4HtF4&ab_channel=JUG.ru

### JET

- [ ] Доклад Гамова на JPoint (устаревший подход): https://www.youtube.com/watch?v=J68pBDicGII&ab_channel=JUG.ru
- [X] Репозиторий Hazelcast Jet: https://github.com/hazelcast/hazelcast-jet