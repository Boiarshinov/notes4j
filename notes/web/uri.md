---
title: "URI"
tags:
  - web
  - network
draft: false
---

# URI

**URI** - Uniform Resource Identifier - унифицированный идентификатор ресурса.

URI пришел на смену [URL](url.md).
В отличие от URL в URI необязательно указание владельца ресурса и протокола.

URI строится по следующему шаблону:
```
[<scheme>:][//<authority>]<path>[?<query>][#<fragment>]
```
- `scheme` - протокол
- `authority` - владелец ресурса. Чаще всего обозначается как host:port
- `path` - путь до ресурса. Всегда начинается с `/` и использует `/` в качестве разделителя
- `query` - параметры запроса
- `fragment` - указатель на составную часть ресурса


---
## К изучению

- [ ] Стандарт [RFC-3986](https://datatracker.ietf.org/doc/html/rfc3986) на URI
- [X] [Wiki URI](https://ru.wikipedia.org/wiki/URI)