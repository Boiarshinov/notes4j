---
title: "Nominatim"
tags:
draft: false
---

**Nominatim** - инструмент для поиска в OpenStreetMap.
Nominatim поддерживает следующие типы запросов к OSM:

- /search - ищет OSM-объекты по имени
- /reverse - ищет OSM-объекты по координатам
- /lookup -
- /status - запрашивает статус сервера
- /deletable - выводит объекты, которые были удалены из OSM, но остались сохранены в Nominatium на случай, если удаление было ошибочным
- /polygons - выводит список сломанных полигонов, обнаруженных Nominatium
- /details - показывает внутреннюю информацию об объекте (используется только при дебаггинге)

---
## Поиск
Поисковый запрос имеет следующую структуру:
```url
https://nominatim.openstreetmap.org/search/<query>?<params>
```

Уточняющие параметры:

- `q=<query>` - что ищем. Используется если поисковый запрос записан в следующей форме:
```url
https://nominatim.openstreetmap.org/search?<params>
```

- `street=<housenumber> <streetname>`
- `city=<city>`
- `county=<county>` - округ
- `state=<state>` -
- `country=<country>` - страна
- `postalcode=<postalcode>` - почтовый индекс

Параметры вывода:

- `format=[html | xml | json | jsonv2 | geojson | geocodejson]` - в каком формате будет возвращен результат (по умолчанию html)

Параметры дополнительных данных:

- `addressdetails=[0 | 1]` - разбивает адрес поэлементно (по умолчанию 0)
- `extratags=[0 | 1]` - добавляет дополнительную информацию при ее наличии (по умолчанию 0)
- `namedetails=[0 | 1]` - включает в результат выдачи список альтернативных имен (по умолчанию 0)

Параметры, ограничивающие вывод:

- `countrycodes=<countrycode>...` - поиск осуществляется только в указанных странах
- `limit=<number>` - ограничивает количество результатов (по умолчанию 10, максимум - 50)

---
## К изучению:

- [X] OSM wiki: https://wiki.openstreetmap.org/wiki/RU:Nominatim
- [ ] API:  https://nominatim.org/release-docs/develop/api/Overview/